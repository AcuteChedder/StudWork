<script>
defineEmits(["close-modal"]);
defineProps({
  modalActive: {
    type: Boolean,
  },
});
</script>

<template>
    <div class=" absolute w-full bg-black backdrop-opacity-30 h-screen top-0 left-0 flex justify-center px-8">
        <div class="p-4 bg-white self-start mt-32 max-w-screen">
            <slot />
            <button @click="$emit('close-modal')" class="text-white mt-8 bg-primary py-2 px-6">Close</button>
        </div>
    </div>
</template>