<script lang="ts">
  let inputTxt: string = '';
  let chats: string[] = [];
  let apiKey: string = "";
  let apiUrl: string = "https://api.upstage.ai/v1/chat/completions";
  let guide : string = "너는 사용자의 여자친구처럼 행동해야 해. 따뜻하고, 다정하게 말해. 사용자의 말에 진심으로 공감해, 긴 대답보다 짧고 자연스럽게, 친구처럼 존댓말은 쓰지 말고 반말로 편하게 대화해.";
  let chatLog : string = "";
  async function send(){
    if(inputTxt.trim() === '') return;
    chats = [...chats, `user : ${inputTxt}`];
    const reply = await sendReq(inputTxt);
    chatLog = chatLog+ `user : ${inputTxt}\nres : ${reply}\n`;
    chats = [...chats, `res: ${reply}`];
    inputTxt = '';
  }
  async function sendReq(reqContent: string){
    const res = await fetch(apiUrl, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${apiKey}`,
      },
      body: JSON.stringify({
        model: 'solar-mini',
        messages: [
          {role : 'system', content: `It was a conversation before ${chatLog} //chat log over// this is your role ${guide}`},
          {role: 'user',content: reqContent},
        ]
      }),
    });
    const data = await res.json();
    return data.choices?.[0]?.message?.content ?? "(답변 없음)";
  }
</script>

<main style="width: 100%;width:100vw">
  <topbar style="display: flex;">
    <p>
      img
    </p>
    <p>
      양현주
    </p>
  </topbar>
  <div class="chat" style="display: flex; flex-direction:column;">
    {#each chats as chat}
      <div class="chat-item">
        <p>{chat}</p>
      </div>
    {/each}
  </div>
  <div class="input">
    <input type="text" bind:value={inputTxt} placeholder="메세지를 입력하세요." id="inputTxt"/>
    <button on:click={send}>전송</button>
  </div>
</main>

<style>
</style>
