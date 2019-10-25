<script>
  export let login;
  import client from '../client';

  const user = {
    email: '',
    password: ''
  };

  async function signUpAndLogin() {
    await client.service('users').create(user);
    login(user);
  }

  async function loginWithGithub() {
    window.location.href = 'http://localhost:3030/oauth/github';
  }
</script>

<style>
form {
  max-width: 400px;
  margin: 0 auto;
  padding: 1rem;
}

input {
  width: 100%;
}

label div, label input {
  margin: 0 0 0.5rem 0;
}

button {
  display: block;
  width: 100%;
  margin: 1rem 0;
}
</style>

<form>
  <h2>Login or Sign up</h2>
  <label>
    <div>Email</div>
    <input bind:value={user.email} name="email" type="email" >
  </label>
  <label>
    <div>Password</div>
    <input bind:value={user.password} name="password" type="password" >
  </label>
  <button on:click={() => login(user)} type="button">
    Login
  </button>
  <button on:click={signUpAndLogin} type="button">
    Sign up and Login
  </button>
  <button on:click={loginWithGithub} type="button">
    Login with Github
  </button>
</form>