<script>
  import data from './data.json';
  let query = '';

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
      <li>
        <a href={r.link} target="_blank" rel="noopener">
          {@html r.highlighted}
        </a>
      </li>
    {/each}
  </ul>
</main>

<style>
  body {
    font-family: "Inter", sans-serif;
    background: #f9fafb;
  }
  main {
    max-width: 600px;
    margin: 10vh auto;
    text-align: center;
  }
  input {
    width: 80%;
    padding: 10px 14px;
    border-radius: 10px;
    border: 1px solid #ddd;
    font-size: 1rem;
    margin-bottom: 20px;
  }
  ul {
    list-style: none;
    padding: 0;
  }
  li {
    background: white;
    margin: 8px 0;
    padding: 10px 14px;
    border-radius: 10px;
    border: 1px solid #e5e7eb;
    box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    transition: 
      border-color 0.15s ease,
      border-width 0.15s ease,
      box-shadow 0.15s ease,
      transform 0.15s ease;
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
</style>
