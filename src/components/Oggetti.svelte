<script>

  import { onMount } from 'svelte';

  
  let items = [];
  let categories = new Set();
  let filterCategory = '';
  let filterPrice = '';

  onMount(async () => {
    const response = await fetch('./src/shopping_trends_updated.csv');
    if (response.ok) {
      const csvText = await response.text();
      items = parseCSV(csvText).slice(0, 3900);
      // Per ogni elemento, aggiungo la sua categoria al Set delle categorie.
      items.forEach(item => categories.add(item.Category));
      // Ordino gli elementi per categoria per facilitare la visualizzazione.
      items.sort((a, b) => a.Category.localeCompare(b.Category));
    } else {
      console.error('Failed to fetch data:', response.status);
    }
  });

  function parseCSV(text) {
    const lines = text.split('\n').map(line => line.trim()).filter(line => line);
    const headers = lines[0].split(',').map(header => header.trim());
    return lines.slice(1).map(line => {
      const data = line.split(',').map(cell => cell.trim());
      return headers.reduce((obj, nextKey, index) => {
        obj[nextKey] = data[index];
        return obj;
      }, {});
    });
  }

  // Filtro e preparo i dati per la tabella in base ai filtri applicati dall'utente.
  $: filteredAndSortedItems = items.filter(item => {
    const passesCategoryFilter = !filterCategory || item.Category === filterCategory;
    const passesPriceFilter = !filterPrice || parseFloat(item['Purchase Amount (USD)']) <= parseFloat(filterPrice);
    return passesCategoryFilter && passesPriceFilter;
  });
</script>

<div>
  <select bind:value={filterCategory}>
    <option value="">All Categories</option>
    <option value="Clothing">Clothing</option>
    <option value="Accessories">Accessories</option>
    <option value="Footwear">Footwear</option>
    <option value="Outerwear">Outerwear</option>
  </select>
  <input type="number" bind:value={filterPrice} placeholder="Max Price (USD)" />
</div>

{#if filteredAndSortedItems.length > 0}
  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>Category</th>
          <th>Item Purchased</th>
          <th>Purchase Amount (USD)</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredAndSortedItems as item}
          <tr>
            <td>{item.Category}</td>
            <td>{item['Item Purchased']}</td>
            <td>{item['Purchase Amount (USD)']}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
{:else}
  <p>No items to display based on filter.</p>
{/if}

<style>
  .table-container {
    overflow-x: auto;
    margin: 0 auto; 
    text-align: center;
  }
  table {
    border-collapse: separate; 
    border-spacing: 0 5px; 
    margin: 0 auto; 
    width: auto; 
  }
  th, td {
    padding: 8px;
    text-align: center; 
  }
  th {
    background-color: #f0f0f0;
  }
  tr:nth-child(odd) td {
    background-color: #fafafa; 
  }
  tr:nth-child(even) td {
    background-color: #f5f5f5;
  }
</style>
