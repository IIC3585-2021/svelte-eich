<script>
  import Login from "./Login.svelte";
  import Logout from "./Logout.svelte";
  import { createEventDispatcher } from "svelte";
  export let user;
  export let login;
  export let logout;

</script>

<style>
.navbar {
    width: 100%;
    height: 12vh;
    position: relative;
    background-color: #174675;
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: flex-start;
    padding: 0 3vw;
    -webkit-box-shadow: 0 10px 6px -6px #777;
    -moz-box-shadow: 0 10px 6px -6px #777;
    box-shadow: 0 10px 6px -6px #777;
}
.left {
  width: 50%;
}

.logo{
  margin: 0;
  color: white;
  font-size: 2vw;
  font-weight: 600;
  text-decoration: none;
  margin-right: 30px;
}

.right {
  width: 50%;
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: flex-end;
} 

.icon-container {
  width: 3.5vw;
  height: 3vw;
  border: #25427b70 solid .5px;
  border-radius: .5vw;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
</style>


  <div class="navbar">
    <div class="left">
      <p class="logo">OpenIA HTML</p>
    </div>
    <div class="right">
      <div >
        {#if $user}
          <Logout on:logout={logout} />
        {:else}
          <Login on:login={login} />
        {/if}
      </div>
    </div>
  </div>