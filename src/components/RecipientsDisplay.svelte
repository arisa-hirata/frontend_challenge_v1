<script lang="ts">
  import { onMount, tick } from 'svelte'

  export let recipients: string[]

  let display = []
  let badge = 1 // Starting at 1 for DOM to assess width of div with badge included
  let element: HTMLDivElement

  async function handleResize() {
    let total = 0
    display = recipients.map(recipient => ` ${recipient}`) // Adding a space before each recipient

    for (let i = display.length - 1; i > 0; i--) {
      await tick()
      const overflow = element.offsetWidth < element.scrollWidth
      if (overflow) display[i] = ' ...'
      if (display[i + 1]?.includes(' ...')) display.pop()
    }
    total += recipients.length - display.length
    if (display[display.length - 1].includes(' ...')) total++
    badge = total
  }

  onMount(() => {
    handleResize()
  })

  window.addEventListener('resize', handleResize)
</script>

<style lang="scss">
  div {
    display: grid;
    grid-template-columns: 1fr min-content;
  }
  .recipient {
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .badge {
    font-size: 16px;
    color: #f0f0f0;
    background-color: $color-primary;
    border-radius: 3px;
    padding: 2px 5px;
    margin-left: 10px;
  }
</style>

<div>
  <div class="recipient" bind:this={element}>{display}</div>
  {#if badge > 0}
    <div class="badge">+{badge}</div>
  {/if}
</div>
