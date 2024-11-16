<script lang="ts">
    import { onMount, onDestroy } from "svelte";
    import { currentUser, pb } from "./pocketbase";

    let msgs: any[] = [];
    let newMsg: string;
    let unsubscribe: () => void;
    onMount(async () => {
        const resultList = await pb.collection("msgs").getList(1, 50, {
            sort: "created",
            expand: "user",
        });
        msgs = resultList.items;

        unsubscribe = await pb
            .collection("msgs")
            .subscribe("*", async ({ action, record }) => {
                if (action == "create") {
                    const user = await pb
                        .collection("users")
                        .getOne(record.user);
                    record.expand = { user };
                    msgs = [...msgs, record];
                }
                if (action == "delete") {
                    msgs = msgs.filter((m) => m.id != record.id);
                }
            });
    });
    onDestroy(() => {
        unsubscribe?.();
    });
    async function sendMsg() {
        if (!$currentUser?.id) {
            alert("your are not loggned in!");
            return;
        }
        const data = {
            text: newMsg,
            user: $currentUser.id,
        };
        const createMsg = await pb.collection("msgs").create(data);
    }
</script>

<div>
    {#each msgs as msg (msg.id)}
        <div class="chat chat-start">
            <div class="chat-header">
                @{msg.expand?.user?.username}
                <time class="text-xs opacity-50">{msg.created}</time>
            </div>
            <div class="chat-bubble">{msg.text}</div>
        </div>
    {/each}
</div>

<form on:submit|preventDefault={sendMsg}>
    <div class="join">
        <textarea
            placeholder="Bio"
            class="textarea textarea-bordered textarea-xs w-full max-w-xs mr-2"
            bind:value={newMsg}
        ></textarea>
        <div class="indicator">
            <span class="indicator-item badge badge-secondary"
                >{$currentUser?.username}</span
            >
            <button class="btn join-item" type="submit">Send</button>
        </div>
    </div>
</form>
