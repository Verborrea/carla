<script>
	import { onMount } from "svelte";

	let state = $state(false);
	let placa = $state("");
	let kilometraje = $state(null);
	let loading = $state(false);
	let _error = $state(false);

	const handleInput = (event) => {
		let value = event.target.value.toUpperCase().replace(/[^A-Z0-9]/g, "");
		if (value.length > 3) {
			value = value.slice(0, 3) + "-" + value.slice(3, 7);
		}
		placa = value;
	};

	const handleSubmit = (e) => {
		e.preventDefault();
		localStorage.setItem('placa', placa);
		state = true
	};

	const formatKilometraje = (event) => {
		let numericValue = event.target.value.replace(/\D/g, "");
		kilometraje = numericValue.replace(/\B(?=(\d{3})+(?!\d))/g, " ");
	}

	const handleSend = async (e) => {
		e.preventDefault();
		const url = "https://script.google.com/macros/s/AKfycbx1Xb496cwL795K5L-X66_1-kzlKrOsLqfPUSJMrfS6mIntot_AiYXIe7KUOCxJalBqDQ/exec";

		const data = {
			placa,
			kilometraje: parseInt(kilometraje.replace(/ /g, ""), 10)
		};

		try {
			loading = true;
			_error = false;

			await fetch(url, {
				method: "POST",
				mode: "no-cors",
				headers: { "Content-Type": "application/json" },
				body: JSON.stringify(data)
			});

			window.location.href = `/gracias?kilometraje=${encodeURIComponent(kilometraje)}`;
		} catch (error) {
			_error = true;
			console.error("Error:", error);
		} finally {
			loading = false;
		}
	}

	onMount(() => {
		placa = localStorage.getItem("placa")
		state = placa
	})
</script>

{#if state}
	<form onsubmit={handleSend}>
		<label for="placa">
			<span>Su placa:</span>
			<input id="placa" type="text" disabled value={placa}>
		</label>
		<button type="button" onclick={() => {
			state = false
		}}>Cambiar Placa</button>
		<label for="kilometraje">
			<span>Ingrese su kilometraje:</span>
			<input
				id="kilometraje"
				type="text"
				inputmode="numeric"
				required
				placeholder="24 500"
				oninput={formatKilometraje}
				bind:value={kilometraje}
			/>
		</label>
		<!-- <button type="button" class="btn-foto">
			<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M14.5 4h-5L7 7H4a2 2 0 0 0-2 2v9a2 2 0 0 0 2 2h16a2 2 0 0 0 2-2V9a2 2 0 0 0-2-2h-3l-2.5-3z"/><circle cx="12" cy="13" r="3"/></svg>
			Tomar Foto
		</button> -->
		<button type="submit" disabled={loading}>
			{#if loading}
			<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="refresh"><path d="M21 12a9 9 0 0 0-9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/><path d="M3 3v5h5"/><path d="M3 12a9 9 0 0 0 9 9 9.75 9.75 0 0 0 6.74-2.74L21 16"/><path d="M16 16h5v5"/></svg>
			Cargando
			{:else if _error}
			Hubo un error :(
			{:else}
			Enviar
			{/if}
		</button>
	</form>
{:else}
	<form onsubmit={handleSubmit}>
		<label for="placa">
			<span>Ingrese su Placa:</span>
			<input
				id="placa"
				type="text"
				minlength="7"
				maxlength="7"
				required
				placeholder="ABC-123"
				oninput={handleInput}
				bind:value={placa}
			/>
		</label>
		<button type="submit">Continuar</button>
	</form>
{/if}

<style>
	.btn-foto {
		background: #469cff;
	}
</style>