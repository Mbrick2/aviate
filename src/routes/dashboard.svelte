<script context="module">
  export async function load({ session }) {
    if (!session?.user) {
      return {
        status: 302,
        redirect: '/login'
      };
    }

    return {
      props: {
        user: session.user
      }
    };
  }
</script>

<script>
  import Header from '$lib/components/Header.svelte';
  import Loader from '$lib/components/Loader.svelte';

  export let user;

  let promise;
  let status = user.status;
  let statusOutput;

  function setStatus() {
    promise = fetch('/api/' + user.username, {
      method: 'POST',
      body: JSON.stringify({
        status
      })
    });
  }

  async function testStatus() {
    promise = fetch('/api/demo', {
      method: 'POST',
      body: JSON.stringify({
        status
      })
    });

    promise
      .then(res => res.json())
      .then(res => statusOutput = res.result);
  }
</script>

<svelte:head>
  <title>{user.username}'s Dashboard - Aviate</title>
</svelte:head>

<Header>
  <span slot="title"><span class="highlight">{user.username}'s</span> Dashboard</span>
  <span slot="subtitle">Customize and set your status here</span>
</Header>

<div class="content">
  <h6>
    Set your status
    <Loader promise={promise}></Loader>
    <span class="tag is-rounded is-{status.length >= 140 ? 'warning' : 'white'}">
      {status.length}/150
    </span>
  </h6>
  <div class="field is-grouped">
    <div class="control is-expanded">
      <input
        type="text"
        bind:value={status}
        class="input is-family-monospace"
        placeholder="Tell people what you're doing, what you're into... anything really."
        maxlength="150"/>
    </div>
    <div class="control buttons has-addons">
      <button on:click={testStatus} class="button">Test</button>
      <button on:click={setStatus} class="button is-primary">Set</button>
    </div>
  </div>

  {#if statusOutput}
    <input class="input is-family-monospace" value={statusOutput} readonly/>
  {/if}

  <h2>Components</h2>
  <p>
    This sections lists the components you can use in your status. Components
    are dynamically updated, meaning each time someone fetches your status they
    will show the correct information. For example, if you use the
    <code>{'{followers}'}</code> component it will always show the amount of
    followers you have on Scratch, though it could be slightly out of date since
    <a href="https://scratchdb.lefty.one/v3/docs/">ScratchDB</a> may not have
    indexed you recently.
  </p>
  <p>
    Components can also have arguments, which can be of two types: a number or a category.
    Numbers are, well, numbers, like 42, -9, and 3.14, but they can also be given another
    component that results in a number (see the examples section at the bottom of the page);
    categories should be given one of these words: "loves", "favorites", "comments",
    "views", "followers", or "following". If you are still confused, here's an example:
    the <code>{'{'}stats <u>category</u>}</code> component lets you replace
    <code><u>category</u></code> with a word shown above to get your statistics in that
    specific category.
  </p>
  <p>
    If you have any ideas for components, please <a href="/#feedback">tell us</a>!
    We want Aviate to be as useful as possible, so hearing your feedback lets
    us make Aviate better for you and everyone else. 😊
  </p>
  <h3>General</h3>
  <ul>
    <li><code>{'{username}'}</code> → your username
    <li><code>{'{id}'}</code> → your ID
    <li><code>{'{country}'}</code> → your country
    <li><code>{'{status}'}</code> → your rank, eg. "Scratcher"
    <li><code>{'{bio}'}</code> → your "About Me"
    <li><code>{'{work}'}</code> → your "What I'm Working On"
    <li><code>{'{joke}'}</code> → a randomly generated programming joke
  </ul>
  <h3>Statistics</h3>
  <ul>
    <li><code>{'{followers}'}</code> → amount of followers
    <li><code>{'{posts}'}</code> → amount of forum posts
    <li><code>{'{'}rank <u>category</u>}</code> → rank in <code><u>category</u></code>
    <li><code>{'{'}stats <u>category</u>}</code> → number of <code><u>category</u></code>
  </ul>
  <h3>Math</h3>
  <ul>
    <li><code>{'{'}add <u>a (number)</u> <u>b (number)</u> ...}</code> → add all of the arguments
    <li><code>{'{'}sub <u>a (number)</u> <u>b (number)</u> ...}</code> → subtract all of the arguments
    <li><code>{'{'}mul <u>a (number)</u> <u>b (number)</u> ...}</code> → multiply all of the arguments
    <li><code>{'{'}div <u>a (number)</u> <u>b (number)</u> ...}</code> → divide all of the arguments
    <li><code>{'{'}pow <u>a (number)</u> <u>b (number)</u>}</code> → raise <code><u>a</u></code> to the power of <code><u>b</u></code>
    <li><code>{'{'}root <u>a (number)</u> <u>b (number)</u>}</code> → find the <code><u>b</u></code>th root of <code><u>a</u></code>
    <li><code>{'{'}random <u>a (number)</u> <u>b (number)</u>}</code> → generate a random number between <code><u>a</u></code> and <code><u>b</u></code>
  </ul>
  <h3>Examples</h3>
  <ul>
    <li><code>I have {'{followers}'} followers on Scratch!</code>
    <li><code>If you add 3 and 4, then multiple that by two, you get {'{mul {add 3 4} 2}'}</code>
    <li><code>Here's a funny joke: {'{joke}'}</code>
    <li><code>Think of a number between 1 and 10. What is {'{random 1 10}'}? If so, you got it right!</code>
  </ul>
</div>