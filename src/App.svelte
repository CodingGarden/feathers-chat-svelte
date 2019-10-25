<script>
	import Login from './views/Login.svelte';
	import Chat from './views/Chat.svelte';

	import client from './client';

	let user;

	async function login(credentials) {
		try {
			if (!credentials) {
				user = await client.reAuthenticate();
			} else {
				user = await client.authenticate({
					strategy: 'local',
					...credentials,
				});
			}
			console.log(user);
		} catch (error) {
			console.log(error);
		}
	}

	async function logout() {
		await client.logout();
		user = null;
	}

	login();
</script>

<style>
</style>

{#if user}
	<Chat logout={logout} />
{:else}
	<Login login={login} />
{/if}

