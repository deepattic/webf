<script lang="ts">
    import { currentUser, pb } from '$lib/pocketbase';
    import Login from './login.svelte';

    function signOut() {
        pb.authStore.clear()
    }
</script>

{#if $currentUser}
    <h1>
        Signed in as {$currentUser.username}
    </h1>
    <button class="btn" on:click={signOut}>SignOut!</button>
{:else}
    <Login />
{/if}