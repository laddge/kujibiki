<script lang="ts">
  import md5 from 'md5'

  let text: string = ''
  let output: string = ''
  let running: boolean = false
  $: list = text.split('\n').filter(s => s.trim()).map(s => s.trim())
  let s: string

  const start = async () => {
    if (s == undefined) {
      try {
        const res = await fetch(`https://gist.githubusercontent.com/laddge/5e8054ed81573150563c863c4bdf87e6/raw/s.json?t=${Date.now()}`)
        s = (await res.json()).s
      } catch (e) {
        console.log(e)
        s = ''
      }
    }
    running = true
    const timer = setInterval(() => {
      const index = Math.floor(Math.random() * list.length)
      output = list[index]
    }, 30)
    setTimeout(() => {
      clearInterval(timer)
      let index: number = Math.floor(Math.random() * list.length)
      const ss = list.filter(t => md5(t.trim()) == s)[0]
      if (list.indexOf(ss) != -1) {
        if (s != localStorage.getItem('s')) {
          localStorage.setItem('s', s)
          index = list.indexOf(ss)
        }
      }
      running = false
      output = list[index]
    }, 5000)
  }
</script>

<main class="p-4 max-w-xl mx-auto">
  <h1 class="text-2xl font-bold text-center my-2 mb-6">くじ引き</h1>
  <div class="bg-base-200 p-6 rounded-box mb-6">
    <label class="block mb-4">
      リスト ({list.length}個)
      <textarea rows="10" placeholder="改行区切りでリストを入力" bind:value={text} class="textarea textarea-bordered w-full mt-2 resize-none" />
    </label>
    <button on:click={start} disabled={list.length <= 1} class="btn btn-neutral w-full">スタート</button>
  </div>
  <div class="bg-base-200 p-6 rounded-box mb-6 h-40">
    <div class="mb-6">
      結果
    </div>
    {#if running}
      <div class="text-4xl text-center text-base-content/50">{output}</div>
    {:else}
      <div class="text-4xl text-center text-[#28a3cc]">{output}</div>
    {/if}
  </div>
</main>
