<script lang="ts">
	import type { TokenizedWord } from '$lib/server-types';
	export let word: TokenizedWord;
	export let simplifiedSentence: string;
	export let isDefinitionQuestion: boolean;
	export let showPinyin: boolean;
	export let showDefinition: boolean;

	const characterSize = '4';
</script>

<div class="word">
	{#if showPinyin || isDefinitionQuestion}
		<span class="word-pinyin">{word.pinyin}</span>
	{/if}
	<span class="word-word">{word.word}</span>
	{#if (showDefinition || !isDefinitionQuestion) && word.primaryDefinition}
		<span class="word-definition" style="width: {word.word.length * characterSize}rem">
			{word.primaryDefinition}
		</span>
	{/if}
</div>

<style>
	.word {
		display: inline-flex;
		flex-direction: column;
		align-items: center;
		margin-bottom: 1em;
	}

	.word-word {
		font-size: 4em;
	}

	.word-definition {
		text-align: center;
	}

	.word-pinyin {
		margin-bottom: -0.5em;
	}
</style>
