<script>
	let { current, max, color } = $props();
</script>

<div class="h-8 w-96 border">
	<div
		style="width: {(current / max) * 100}%"
		class="h-full {color} transition-all duration-100"
	></div>
</div>