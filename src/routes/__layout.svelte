<script>
	const routes = [
		{ href: '/', name: 'Home' },
		{ href: '/examples', name: 'Examples' }
	];
</script>

<nav>
	{#each routes as route}
		<a href={route.href}>{route.name}</a>
	{/each}
</nav>

<main>
	<slot />
</main>

<style>
	nav {
		padding: 5px 0;
	}
	a {
		margin-right: 1rem;
	}
</style>
