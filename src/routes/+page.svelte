<script lang="ts">
	import { type DateValue, DateFormatter, getLocalTimeZone } from '@internationalized/date';

	import { Textarea } from '$lib/components/ui/textarea';

	const df = new DateFormatter('en-US', {
		dateStyle: 'long'
	});

	let value: DateValue | undefined = undefined;
</script>

<div class="absolute bottom-10 left-0 w-1/2 translate-x-1/2">
	<Textarea />
</div>
