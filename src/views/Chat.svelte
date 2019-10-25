<script>
  export let logout;
  import { onMount, tick } from 'svelte';
  import client from '../client';
  let users = [];
  let messages = [];
  let newMessage = '';
  let messagesElement;

  async function addMessage() {
    await client.service('messages').create({
      text: newMessage
    });
    newMessage = '';
  }

  onMount(async () => {
    ({ data: users } = await client.service('users').find());
    ({ data: messages } = await client.service('messages').find({
      query: {
        $sort: { createdAt: -1 },
        $limit: 25
      }
    }));
    messages = messages.reverse();

    await tick();
    messagesElement.scrollTop = messagesElement.scrollHeight - messagesElement.clientHeight;
    
  });

  client.service('messages').on('created', async (message) => {      
    messages[messages.length] = message;
    await tick();
    messagesElement.scrollTop = messagesElement.scrollHeight - messagesElement.clientHeight;
  });

  client.service('users').on('created', (user) => {
    users[users.length] = user;
  });
</script>

<style>
  main {
    display: flex;
    height: 100%;
    position: relative;
    align-items: stretch;
  }

  .user-list {
    flex: 1;
    display: flex;
    flex-direction: column;
    padding: 1rem;
    height: 100%;
    border-right: 1px solid hsl(0, 0%, 80%);
    background: hsl(0, 0%, 92%);
  }

  .users {
    flex-grow: 1;
  }

  .logout {
    width: 100%;
    padding-top: 0.5rem;
    padding-bottom: 0.5rem;
    margin-bottom: 2rem;
  }

  .message-list {
    flex: 4;
    display: flex;
    flex-direction: column;
    align-items: stretch;
    height: 100%;
  }

  .messages {
    overflow: scroll;
    flex-grow: 1;
  }

  .message-form {
    height: 10%;
    margin: 0.3rem;
  }

  .message-form input {
    width: 84%;
    margin: 0;
    box-sizing: border-box;
    padding: 0.7rem 1rem;
  }

  .message-form button {
    width: 15%;
    margin: 0;
    box-sizing: border-box;
    /* background-color: #0B3954; */
    background-color: #66b99e;
  }

  .user-count {
    margin-bottom: 0.5em;
  }

  .user-count-num {
    font-weight: bold;
    margin-right: 0.5rem;
  }

  .user {
    display: flex;
    align-items: center;
  }

  .user p {
    font-weight: bold;
    opacity: .7;
  }

  .user img {
    width: 25px;
    height: 25px;
    margin-right: 0.5rem;
    border-radius: 50%;
  }

  .message {
    padding: 0 15px 5px;
    border-radius: 10px;
    background-color: hsl(0, 0%, 100%);
  }

  .message:nth-child(odd) {
    background-color: hsl(0, 0%, 97%);
  }

</style>

<main>
  <aside class="user-list">
    <span class="user-count"><span class="user-count-num">{users.length}</span> users</span>
    <div class="users">
      {#each users as user}
        <div class="user">
          {#if user.avatar}
            <img src={user.avatar} alt={user.email}>
          {/if}
          <p>{user.email}</p>
        </div>
      {/each}
    </div>
    <button class="logout" on:click={logout}>Log Out</button>
  </aside>
  <div class="message-list">
    <div class="messages" bind:this={messagesElement}>
      {#each messages as message}
        <div class="message">
          <div class="user">
            <img src={message.user.avatar} alt={message.user.email}>
            <p>{message.user.email}</p>
          </div>
          <p>{message.text}</p>
        </div>
      {/each}
    </div>
    <form class="message-form" on:submit|preventDefault={addMessage}>
      <input bind:value={newMessage} name="message" placeholder="Send a message...">
      <button>Send Message</button>
    </form>
  </div>
</main>