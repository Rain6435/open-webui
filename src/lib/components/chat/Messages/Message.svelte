<script lang="ts">
	import { toast } from 'svelte-sonner';

	import { tick, getContext, onMount, onDestroy, createEventDispatcher } from 'svelte';
	const dispatch = createEventDispatcher();
	const i18n = getContext('i18n');

	import { settings, mobile } from '$lib/stores';
	import { copyToClipboard } from '$lib/utils';

	import MultiResponseMessages from './MultiResponseMessages.svelte';
	import ResponseMessage from './ResponseMessage.svelte';
	import UserMessage from './UserMessage.svelte';

	// Fixed width system - no resizing
	let bubbleElement;
	const defaultWidth = '1152px'; // Always use prompt width

	export let chatId;
	export let selectedModels = [];
	export let idx = 0;

	export let history;
	export let messageId;

	export let user;

	export let setInputText: Function = () => {};
	export let gotoMessage;
	export let showPreviousMessage;
	export let showNextMessage;
	export let updateChat;

	export let editMessage;
	export let saveMessage;
	export let deleteMessage;
	export let rateMessage;
	export let actionMessage;
	export let submitMessage;

	export let regenerateResponse;
	export let continueResponse;
	export let mergeResponses;

	export let addMessages;
	export let triggerScroll;
	export let readOnly = false;
	export let topPadding = false;
</script>

<div
	class="{($settings?.widescreenMode ?? null) ? 'max-w-full' : 'max-w-6xl'} {$mobile
		? 'px-1'
		: 'px-2.5'} mx-auto"
>
	<div
		bind:this={bubbleElement}
		data-message-bubble
		class="relative flex flex-col justify-between {$mobile
			? 'px-2'
			: 'px-5'} mb-3 w-full rounded-lg group overflow-hidden"
		style="
			min-height: 60px !important; 
			contain: layout !important; 
			overflow-wrap: break-word !important;
			word-break: break-word !important;
			width: {defaultWidth} !important;
			max-width: {defaultWidth} !important;
			height: auto !important;
		"
	>
		{#if history.messages[messageId]}
			{#if history.messages[messageId].role === 'user'}
				<UserMessage
					{user}
					{chatId}
					{history}
					{messageId}
					isFirstMessage={idx === 0}
					siblings={history.messages[messageId].parentId !== null
						? (history.messages[history.messages[messageId].parentId]?.childrenIds ?? [])
						: (Object.values(history.messages)
								.filter((message) => message.parentId === null)
								.map((message) => message.id) ?? [])}
					{gotoMessage}
					{showPreviousMessage}
					{showNextMessage}
					{editMessage}
					{deleteMessage}
					{readOnly}
					{topPadding}
				/>
			{:else if (history.messages[history.messages[messageId].parentId]?.models?.length ?? 1) === 1}
				<ResponseMessage
					{chatId}
					{history}
					{messageId}
					{selectedModels}
					isLastMessage={messageId === history.currentId}
					siblings={history.messages[history.messages[messageId].parentId]?.childrenIds ?? []}
					{setInputText}
					{gotoMessage}
					{showPreviousMessage}
					{showNextMessage}
					{updateChat}
					{editMessage}
					{saveMessage}
					{rateMessage}
					{actionMessage}
					{submitMessage}
					{deleteMessage}
					{continueResponse}
					{regenerateResponse}
					{addMessages}
					{readOnly}
					{topPadding}
				/>
			{:else}
				<MultiResponseMessages
					bind:history
					{chatId}
					{messageId}
					{selectedModels}
					isLastMessage={messageId === history?.currentId}
					{setInputText}
					{updateChat}
					{editMessage}
					{saveMessage}
					{rateMessage}
					{actionMessage}
					{submitMessage}
					{deleteMessage}
					{continueResponse}
					{regenerateResponse}
					{mergeResponses}
					{triggerScroll}
					{addMessages}
					{readOnly}
					{topPadding}
				/>
			{/if}
		{/if}
	</div>
</div>