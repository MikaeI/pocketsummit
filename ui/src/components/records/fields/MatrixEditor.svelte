<script lang="ts">
  export let value: Record<string, Record<string, number>>;
  export let readonly = false;
  export let onChange: (v: Record<string,Record<string,number>>) => void;

  const itemBands = Object.keys(value ?? {});
  const measureBands = Object.keys(value?.[itemBands[0]] ?? {});

  function setPrice(iBand: string, mBand: string, ev: Event) {
    const input = ev.target as HTMLInputElement;
    const v = Number(input.value);
    const next = structuredClone(value);
    next[iBand][mBand] = isNaN(v) ? 0 : v;
    onChange(next);
  }
</script>

<table class="w-full text-sm border-collapse">
  <thead>
    <tr class="bg-gray-100 sticky top-0">
      <th class="p-2 border"># Items</th>
      {#each measureBands as m}
        <th class="p-2 border text-center">{m}</th>
      {/each}
    </tr>
  </thead>
  <tbody>
    {#each itemBands as i}
      <tr>
        <td class="p-2 border font-semibold">{i}</td>
        {#each measureBands as m}
          <td class="border p-0">
            <input
              type="number"
              min="0"
              class="w-full p-2 text-center focus:outline-none"
              disabled={readonly}
              value={value[i][m]}
              on:input={(e) => setPrice(i, m, e)}
            />
          </td>
        {/each}
      </tr>
    {/each}
  </tbody>
</table>
