<script>
	import { page } from '$app/stores';

	const kilometraje = $page.url.searchParams.get('kilometraje')
</script>

<main>
	<h1>Gracias!</h1>
	<p>La información se envió correctamente.</p>
	<p class="big"><strong>{kilometraje} km</strong></p>
</main>

<style>
	main {
		height: 100dvh;
		padding: 24px;
		gap: 24px;
		display: flex;
		flex-direction: column;
		text-align: center;
		align-items: center;
		justify-content: center;
	}
	p {
		color: gray;
	}
	.big {
		color: black;
		font-size: 2em;
	}
</style>