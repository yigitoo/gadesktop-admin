<script>
    import { isLogged } from "../get_stores.mjs";
    import { is_logged } from "../stores.mjs";
    export let logged;
    const logout = () => is_logged.set(false);
</script>
  
  <nav>
    <div class="inner">
      <ul class="navbar-list">
        {#if logged}
            <li>
                <!-- svelte-ignore a11y-invalid-attribute -->
                <a href='#' style="color: green;">Şikayetler</a>
            </li>
            <li>
                <!-- svelte-ignore a11y-invalid-attribute -->
                <a href='#' on:click={() => logout()}>Çıkış yap!</a>
            </li>
        {:else}
            <li>
                <!-- svelte-ignore a11y-invalid-attribute -->
                <a href='#' style="color: green;">Giriş yap!</a>
            </li>
        {/if}
      </ul>
    </div>
</nav>
<style>
nav {
    background-color: rgba(0, 0, 0, 0.8);
    font-family: "Helvetica Neue", "Helvetica", "Arial", sans-serif;
    height: 45px;
}

.inner {
    max-width: 980px;
    padding-left: 20px;
    padding-right: 20px;
    margin: auto;
    box-sizing: border-box;
    display: flex;
    align-items: center;
    height: 100%;
}


.navbar-list {
    display: none;
    width: 100%;
    justify-content: space-between;
    margin: 0;
    padding: 0 40px;
}


.navbar-list li {
    list-style-type: none;
    position: relative;
}

.navbar-list li:before {
    content: "";
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 1px;
    background-color: #424245;
}

.navbar-list a {
    color: #fff;
    text-decoration: none;
    display: flex;
    height: 45px;
    align-items: center;
    padding: 0 10px;
    font-size: 13px;
}

@media only screen and (min-width: 767px) {

    .navbar-list {
    display: flex;
    padding: 0;
    }

    .navbar-list a {
    display: inline-flex;
    }
}
</style>
