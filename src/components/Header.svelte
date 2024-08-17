<script>
	export let y;
	let tabs = [
		{ name: 'Rating', link: '#rating' },
		{ name: 'Events', link: '#events' },
		{ name: 'Partibase', link: '#partibase' },
		{ name: 'Janus Carlsen', link: '#janusCarlsen' }
	];
</script>

<header
	class={'sticky z-[10] top-0 duration-200 px-6 flex items-center justify-between border border-solid ' +
		(y > 0 ? 'py-4 bg-slate-950 border-violet-950' : 'py-6 bg-transparent border-transparent ')}
>
	<h1>
		<b> Janus: </b> To tårn
	</h1>
	<div>
		{#each tabs as tab, index}
			<a>
				<p>{tab.name}</p>
			</a>
		{/each}
	</div>
</header>
