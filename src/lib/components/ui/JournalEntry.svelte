<script lang="ts">
	export let title: string;
	export let date: Date;
	export let last: boolean;
</script>

<main class="my-5">
	<div class="opacity-60">
		{date.toLocaleDateString()} – <span class="text-xs">{date.toLocaleTimeString()}</span>
	</div>

	<div class="text-xl font-semibold">
		{@html title}
	</div>
</main>
