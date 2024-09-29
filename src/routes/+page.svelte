<script lang="ts">
	import { onMount, createEventDispatcher } from 'svelte';
	import type { PageData } from '$lib/page-data-types';
	import nlp from 'compromise';
	import Quiz from './Quiz.svelte';
	import Word from './Word.svelte';

	export let data: PageData;
	const { translation, seed } = data;

	let useSimplified = true;
	let isDefinitionQuestion = true;
	let blurredBackgroundImage = '';
	let showPinyin = false;
	let showDefinition = false;

	console.log(seed);

	console.log(data);

	onMount(() => {
		loadImages();
	});

	function loadImages() {
		const doc = nlp(translation);
		const nouns = doc.nouns().out('array');
		if (nouns.length > 0) {
			const firstNoun = encodeURIComponent(nouns[0]);
			blurredBackgroundImage = `https://loremflickr.com/1280/827/${firstNoun}`;
		}
	}

	function handleQuizTypeChange(event: CustomEvent<boolean>) {
		isDefinitionQuestion = event.detail;
	}

	function togglePinyin() {
		showPinyin = !showPinyin;
	}

	function toggleDefinition() {
		showDefinition = !showDefinition;
	}
</script>

<main>
	<img src={blurredBackgroundImage} alt="Blurred Background" class="blur-overlay" />
	<div class="content">
		<div class="sentence">
			{#if useSimplified}
				{#each data.tokenized.simplified as word}
					<Word
						{word}
						simplifiedSentence={data.simplifiedSentence}
						{isDefinitionQuestion}
						{showPinyin}
						{showDefinition}
					/>
				{/each}
			{:else}
				{#each data.tokenized.traditional as word}
					<Word
						{word}
						simplifiedSentence={data.simplifiedSentence}
						{isDefinitionQuestion}
						{showPinyin}
						{showDefinition}
					/>
				{/each}
			{/if}
		</div>

		<p class="translation">{translation}</p>

		<Quiz {data} {isDefinitionQuestion} on:quizTypeChange={handleQuizTypeChange} />

		<div class="controls">
			<button on:click={togglePinyin}>{showPinyin ? 'Hide' : 'Show'} Pinyin</button>
			<button on:click={toggleDefinition}>{showDefinition ? 'Hide' : 'Show'} Definition</button>
		</div>
	</div>
</main>

<style>
	main {
		position: relative;
		min-height: 100svh;
		overflow: hidden;
	}

	.blur-overlay {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		object-fit: cover;
		z-index: 0;
		filter: brightness(0.7) blur(10px);
	}

	.content {
		position: relative;
		z-index: 1;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		min-height: 100svh;
		padding: 1em;
		color: white;
		text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
		box-sizing: border-box;
	}

	.translation {
		font-style: italic;
		margin-bottom: 2rem;
	}

	.controls {
		display: flex;
		gap: 1rem;
	}

	button {
		padding: 0.5rem 1rem;
		background-color: rgba(255, 255, 255, 0.2);
		border: none;
		border-radius: 4px;
		color: white;
		cursor: pointer;
		transition: background-color 0.3s;
	}

	button:hover {
		background-color: rgba(255, 255, 255, 0.3);
	}
</style>
