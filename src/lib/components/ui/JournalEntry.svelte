<script lang="ts">
	export let title: string;
	export let date: Date;
</script>

<li class="flex flex-col rounded-xl border-b px-1 py-2">
	<div class="text-xl">
		{@html title}
	</div>

	<div class="self-end tabular-nums opacity-60">
		{date.toLocaleDateString()} – <span class="text-xs">{date.toLocaleTimeString()}</span>
	</div>
</li>
