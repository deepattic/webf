<script lang="ts">
    import type { PageData } from './$types';
    import { currentUser, pb } from '$lib/pocketbase';

    let username: string;
    let password: string;

    async function login() {
        await pb.collection('users').authWithPassword(username,password);
    }

    async function signUp() {
        try {
        const data = {
            username,
            password,
            passwordConfirm: password,
            name: 'hello world',
        };
        const createdUser = await pb.collection('users').create(data);
        await login();
    } catch (err) {
        console.error(err)
    }
    }

    function signOut() {
        pb.authStore.clear();
    }

</script>

<div class="w-full h-screen flex justify-center items-center">
    <form class="bg-base-300 shadow-md rounded w-96 h-96 flex flex-col justify-center items-center " on:submit|preventDefault >
        <h2 class="mb-4"> Login or SignUp!</h2>
        <label class="input input-bordered flex items-center gap-2 mb-2">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 16 16"
              fill="currentColor"
              class="h-4 w-4 opacity-70">
              <path
                d="M8 8a3 3 0 1 0 0-6 3 3 0 0 0 0 6ZM12.735 14c.618 0 1.093-.561.872-1.139a6.002 6.002 0 0 0-11.215 0c-.22.578.254 1.139.872 1.139h9.47Z" />
            </svg>
            <input type="text" class="grow" placeholder="Username" required bind:value={username} />
          </label>
          <label class="input input-bordered flex items-center gap-2 mb-6">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 16 16"
              fill="currentColor"
              class="h-4 w-4 opacity-70">
              <path
                fill-rule="evenodd"
                d="M14 6a4 4 0 0 1-4.899 3.899l-1.955 1.955a.5.5 0 0 1-.353.146H5v1.5a.5.5 0 0 1-.5.5h-2a.5.5 0 0 1-.5-.5v-2.293a.5.5 0 0 1 .146-.353l3.955-3.955A4 4 0 1 1 14 6Zm-4-2a.75.75 0 0 0 0 1.5.5.5 0 0 1 .5.5.75.75 0 0 0 1.5 0 2 2 0 0 0-2-2Z"
                clip-rule="evenodd" />
            </svg>
            <input type="password" class="grow" required minlength="8" bind:value={password} />
          </label>
      <div class="flex items-center justify-between">
        <button class=" btn btn-primary" on:click={login}>Login</button>
        <button class="btn btn-link" on:click={signUp}>Don't have an account?</button>
      </div>
    </form>
  </div>

