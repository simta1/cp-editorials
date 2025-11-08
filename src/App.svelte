<script>
  import data from './data.json';
  let query = '';

  $: data.sort((a, b) => a.name.localeCompare(b.name));

  $: results = data
    .filter(d => matchesAllWords(d.name, query))
    .map(d => ({
      ...d,
      highlighted: highlightMatch(d.name, query)
    }));

  function matchesAllWords(text, query) {
    if (!query) return true;
    const words = query.trim().toLowerCase().split(/\s+/);
    const lowerText = text.toLowerCase();
    return words.every(w => lowerText.includes(w));
  }

  function highlightMatch(text, query) {
    if (!query) return text;
    const words = query.trim().split(/\s+/).filter(Boolean);
    if (words.length === 0) return text;
    const pattern = words.map(escapeRegExp).join('|');
    const regex = new RegExp(`(${pattern})`, 'gi');
    return text.replace(regex, '<mark>$1</mark>');
  }

  function escapeRegExp(str) {
    return str.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
  }
</script>

<main>
  <h1>CP Editorials</h1>
  <input bind:value={query} placeholder="Search contest..." autofocus />
  <ul>
    {#each results as r}
      {#if Array.isArray(r.link)}
        <li>
          <div>{@html r.highlighted}</div>
          <div class="multi-links">(
            {#each r.link as l, i}
              <a href={l.url} target="_blank" rel="noopener">{l.label}</a>{#if i < r.link.length - 1},{' '}{/if}
            {/each}
          )</div>
        </li>
      {:else}
        <li on:click={() => window.open(r.link, '_blank', 'noopener')}>
          <div class="single-link">
            {@html r.highlighted}
          </div>
        </li>
      {/if}
    {/each}
  </ul>
</main>

<style>
  :root {
    --pane: 640px;
  }
  *, *::before, *::after {
    box-sizing: border-box;
  }
  html {
    scrollbar-gutter: stable both-edges;
  }
  body {
    margin: 0;
    font-family: "Inter", sans-serif;
    background: #f9fafb;
  }
  main {
    width: 100%;
    max-width: var(--pane);
    margin: 5vh auto;
    padding-left: 16px;
    padding-right: 16px;
    text-align: center;
  }
  input {
    width: 100%;
    display: block;
    padding: 10px 14px;
    border-radius: 10px;
    border: 1px solid #ddd;
    font-size: 1rem;
    margin-bottom: 20px;
  }
  ul {
    list-style: none;
    padding: 0;
    margin: 0;
  }
  li {
    width: 100%;
    background: white;
    margin: 8px 0;
    padding: 10px 14px;
    border-radius: 10px;
    border: 1px solid #e5e7eb;
    box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    transition: border-color 0.15s ease, border-width 0.15s ease, box-shadow 0.15s ease, transform 0.15s ease;
    cursor: pointer;
    overflow-wrap: anywhere;
  }
  li:hover {
    border-color: #3b82f6;
    border-width: 1px;
    box-shadow: 0 3px 8px rgba(59,130,246,0.25);
    transform: translateY(-2px);
  }
  a {
    text-decoration: none;
    color: #0070f3;
    font-weight: 500;
  }
  mark {
    background: #ffeb3b;
    color: inherit;
    font-weight: 600;
    border-radius: 2px;
    padding: 0 2px;
  }
  .multi-links {
    margin-top: 4px;
    font-size: 0.9rem;
    color: #374151;
  }
  .multi-links a {
    color: #2563eb;
    text-decoration: none;
  }
  .multi-links a:hover {
    text-decoration: underline;
  }
  li a {
    cursor: pointer;
  }
  .single-link {
    color: #0070f3;
    font-weight: 500;
  }
  h1,
  .single-link,
  .multi-links,
  li {
    user-select: none;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
  }
  h1 {
	font-size: 2rem;
  }
</style>
