<script>
	import { onMount, onDestroy } from 'svelte';

	/** @type {string} */
	let server_time;
	
	/** @type {any} */
	let unsub = null

	function subscribe() {
		// Do 5 changes in the file in dev mode, and the server is hanging.
		// We can see 2 lines in the network tab (chrome) 
		// - serviceworker
		// - "normal"
		// on Each change, the serviceworker has a unsubscribe, but not the other one!
		console.log(`sub YES!   012345`);
		const sse = new EventSource('/sse');
		sse.onmessage = (e) => (server_time = e.data);
		return () => {
			console.log(`sub NO !`);
			sse.close();
			unsub = null
		};
	}

	onMount(()=>{
		unsub = subscribe()
	});

	onDestroy(()=>{
		if(unsub){
			unsub()
		}
	})

	const buttonUnSub = ()=>{
		// this should unsub serviceworker & normal!
		// But it's not doingthe "normal"!
		if(unsub){
			unsub()
		}
	}

</script>

<h2>SSE 🚀</h2>
<pre>{server_time}</pre>

<button on:click={buttonUnSub}>Unsub</button>