<svelte:head>
    <title>My page</title>
</svelte:head>

<script>
    import projects from "$lib/projects.json";
    import Project from "$lib/Project.svelte";

    import { onMount } from "svelte";

    let githubData = null;
    let loading = true;
    let error = null;

    onMount(async () => {
        try {
            const response = await fetch("https://api.github.com/users/Leandr0ER");
            githubData = await response.json();
        } catch (err) {
            error = err;
        }
        loading = false;
    });

    </script>
<h1> Leiton Estrada</h1>
   
<img src="./images/Imagen001.jpg" alt="mike" width="500px">
    
<p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed lacinia accumsan euismod.
    Nunc consequat accumsan mi, vitae scelerisque lectus. Aenean aliquam nibh vestibulum tempus pharetra.
    Curabitur finibus risus nibh, sit amet bibendum ipsum placerat quis. Nam quis nulla id ipsum maximus molestie.
    In dolor magna, facilisis eget mi ut, condimentum sollicitudin neque.
    Sed eu molestie ipsum, a aliquam risus. Morbi consequat imperdiet elit, convallis viverra mi faucibus ac.
    Nulla facilisi. Proin et auctor justo. Etiam tellus felis, fermentum et ipsum eu, porta aliquet tortor.
</p>

<h2>Latest projects</h2>
<div class="projects">
{#each projects.slice(0, 3) as p}
    <Project data={p} hLevel="3" />
{/each}

{#if loading}
    <p>Loading...</p>
{:else if error}
    <p class="error">Something went wrong: {error.message}</p>
{:else}
    <section>
        <h2>My GitHub Stats</h2>
        <dl>
            <dt>Followers</dt>
            <dd>{githubData.followers}</dd>
            <dt>Following</dt>
            <dd>{githubData.following}</dd>
            <dt>Public Repositories</dt>
            <dd>{githubData.public_repos}</dd>
        </dl>
    </section>
{/if}


</div>
