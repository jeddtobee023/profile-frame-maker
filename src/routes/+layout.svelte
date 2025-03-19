<script>
	import "../app.css";
	import { page } from '$app/stores';
</script>

<svelte:head>
	<link href="https://fonts.googleapis.com/css?family=Inter" rel="stylesheet" />
	<meta property="og:title" content="Profile Frame Maker">
	<meta property="og:type" content="article" />
	<meta property="og:url" content={$page.url.href}>
	<meta property="og:image" content={$page.url.hostname + "/cover-photo.jpg"}>
	<meta property="og:description" content="Get your Frame for your profile picture!">
	<meta property="og:site_name" content="Profile Frame Maker">
</svelte:head>

<main class="bg-slate-800">
	<slot />
</main>
