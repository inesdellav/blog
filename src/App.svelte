<script lang="ts">
  import { onMount } from "svelte";
  import PwaUpdate from "./PwaUpdate.svelte";
  import Post from "./routes/Post.svelte";
  import Seo from "./Seo.svelte";
  import { getPost, posts } from "./lib/posts";
  import {
    currentRoute,
    handleLinkClick,
    withBase,
    type Route,
  } from "./lib/router";

  let route = $state<Route>({ name: "home" });
  let activePost = $derived(route.name === "post" ? getPost(route.slug) : undefined);

  onMount(() => {
    route = currentRoute();
    const onPopState = () => {
      route = currentRoute();
      window.scrollTo({ top: 0 });
    };
    const onClick = (event: MouseEvent) => handleLinkClick(event);
    window.addEventListener("popstate", onPopState);
    document.addEventListener("click", onClick);
    return () => {
      window.removeEventListener("popstate", onPopState);
      document.removeEventListener("click", onClick);
    };
  });

</script>

{#if route.name === "post"}
  {#if activePost}
    <Seo
      title={activePost.metadata.title}
      description={activePost.metadata.description}
      path={`/post/${activePost.slug}/`}
    />
    <Post post={activePost} />
  {:else}
    <Seo title="Post non trovato" path="/" noindex={true} />
    <main class="page">
      <p class="eyebrow">404</p>
      <h1 class="title">Questo post non c'è ancora. Forse arriverà prima o poi</h1>
      <div class="actions">
        <a href={withBase("/")} class="btn-primary">Torna alla home</a>
      </div>
    </main>
  {/if}
{:else if route.name === "not-found"}
  {@const missingPath = route.path}
  <Seo title="Page not found" path={missingPath} noindex={true} />
  <main class="page">
    <p class="eyebrow">404</p>
    <h1 class="title">Page not found.</h1>
    <p class="lede">No page at <code class="code">{missingPath}</code>.</p>
    <div class="actions">
      <a href={withBase("/")} class="btn-primary">Back home</a>
    </div>
  </main>
{:else}
  <Seo path="/" />
  <!-- MAIN PAGE -->
  <main class="page">
    <h1 class="title">Colori Mancanti</h1>

    <!-- navbar -->
     <header>
        <h1>Colori Mancanti</h1>
        <nav>
            <ul>
                <li><a href="/">Home</a></li>
                <li><a href="/hello">About Me</a></li> <!-- SISTEMA href! -->
                <li><a href="/blog">Blog Posts</a></li>
               <!--  <li><a href="#contact">Contact</a></li> -->
            </ul>
        </nav>
    </header>


    <!-- lista dei post, in ordine cronologico inverso-->
    <!-- VEDI TU SE ORDINARLI PER ARGOMENTO PRIMA O POI -->
    <ul class="steps">
      {#each posts as post (post.slug)}
        <li>
          <a href={withBase(`/post/${post.slug}`)} class="link-inline">
            {post.metadata.title}
          </a>
          <span class="step-body">
            {post.metadata.description ?? post.slug}
            {#if post.metadata.date} · {post.metadata.date}{/if}
          </span>
        </li>
      {/each}
    </ul>

    <!-- footer -->
     <footer>
        <p>&copy; 2026 Colori Mancanti | Fatto con il cuore</p>
    </footer>
  </main>
{/if}

<PwaUpdate />
