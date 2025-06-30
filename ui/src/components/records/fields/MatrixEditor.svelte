<script lang="ts">
  export let value: Record<string, Record<string, number>>;
  export let readonly = false;
  export let onChange: (v: Record<string,Record<string,number>>) => void;

  import { onMount } from "svelte";

  const defaultItems = [
    "1-3",
    "4-6",
    "7-15",
    "15-25",
    "25-50",
    "50+",
  ];
  const defaultMeasures = [
    "40-64",
    "65-80",
    "81-100",
    "101-120",
    "121-140",
    "141-160",
    "161-199",
    "200-240",
  ];

  // ensure the matrix always has some data to render
  onMount(() => {
    if (!value || Object.keys(value).length === 0) {
      const next: Record<string, Record<string, number>> = {};
      for (const i of defaultItems) {
        next[i] = {};
        for (const m of defaultMeasures) {
          next[i][m] = 0;
        }
      }
      onChange(next);
    }
  });

  let itemBands: string[] = [];
  let measureBands: string[] = [];

  $: itemBands = Object.keys(value ?? {});
  $: if (itemBands.length === 0) itemBands = [...defaultItems];

  $: measureBands = Object.keys(value?.[itemBands[0]] ?? {});
  $: if (measureBands.length === 0) measureBands = [...defaultMeasures];

  function setPrice(iBand: string, mBand: string, ev: Event) {
    const input = ev.target as HTMLInputElement;
    const v = Number(input.value);
    const next = structuredClone(value ?? {});
    if (!next[iBand]) {
      next[iBand] = {};
    }
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
              value={value?.[i]?.[m] ?? 0}
              on:input={(e) => setPrice(i, m, e)}
            />
          </td>
        {/each}
      </tr>
    {/each}
  </tbody>
</table>
