<script lang="ts">
	import { afterUpdate } from 'svelte';

	import Send from 'lucide-svelte/icons/send';
	import Trash from 'lucide-svelte/icons/trash';
	import winkNLP from 'wink-nlp';
	import model from 'wink-eng-lite-web-model';

	import { list } from '../stores.js';

	import type { HTMLTextareaAttributes } from 'svelte/elements';
	import Input from '@/components/ui/input/input.svelte';
	import Button from '@/components/ui/button/button.svelte';
	import JournalEntry from '@/components/ui/JournalEntry.svelte';
	import CalendarMonths from '@/components/ui/calendar/calendar-months.svelte';

	const nlp = winkNLP(model);
	const its = nlp.its;
	const as = nlp.as;

	let value: string;

	let journal: HTMLUListElement;
	let lastElement: HTMLUListElement;
	let textArea: HTMLTextareaAttributes;

	const monthColor = {
		aug: '#FF0000',
		sep: 'green'
	};

	const isDateTime = {
		DATE: true,
		TIME: true
	};

	let items: string[] = [];
	function processItem() {
		const doc = nlp.readDoc(value);

		doc
			// extract entities,
			.entities()
			// filter DATE & TIME,
			.filter((e) => isDateTime[e.out(its.type)])
			// and finally markup with bold tag with class as entity type.
			.each((e) => {
				console.log(e.out(its.value));
				e.markup(
					`<span class="bg-[${monthColor[e.out(its.value)]}] px-1.5 py-0.5 rounded-md text-primary-foreground" id='${e.out(its.type)}'>`,
					`</span>`
				);
			});

		$list.push(doc.out(its.markedUpText));
		$list = $list;
		value = ''.trim();

		// lastElement.scrollIntoView(true);
	}

	function sendItem(event: KeyboardEvent) {
		if (value === '' || value === undefined) return;
		if (event.key === 'Enter') {
			processItem();
		}
	}

	function sendItemButton(event: Event) {
		if (value === '' || value === undefined) return;
		processItem();
	}

	function resetStore() {
		$list = [];
	}

	// Either afterUpdate()
	afterUpdate(() => {
		if ($list) scrollToBottom(journal);
	});

	// $: if ($list && journal) {
	// 	scrollToBottom(journal);
	// }

	const scrollToBottom = (node: HTMLUListElement) => {
		node.scroll({ top: node.scrollHeight, behavior: 'smooth' });
	};
</script>

<head>
	<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0" />
</head>

<main class="mx-auto my-5 w-[90%] md:my-20 md:w-1/2">
	<ul
		bind:this={journal}
		class="my-10 flex h-[600px] list-inside list-disc flex-col space-y-2 overflow-y-scroll [&_li:last-child]:border-0"
	>
		{#each $list as item}
			<JournalEntry title={item} date={new Date()} />
		{/each}
	</ul>
	<div class="bottom-1 w-full md:bottom-10">
		<div class="my-2 flex gap-3">
			<Input bind:this={textArea} bind:value on:keydown={sendItem} />
			<Button on:click={sendItemButton} variant="outline">
				<Send strokeWidth={2.4} />
			</Button>
		</div>

		<Button variant="destructive" class="font-bold" on:click={resetStore}>
			<Trash strokeWidth={3} size={15} class="mr-3" />
			Reset
		</Button>
	</div>
</main>
