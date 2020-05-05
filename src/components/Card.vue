<template>
  <div
    class="flex items-center justify-center rounded-lg shadow-md px-8 py-8 md:px-16 my-3 text-xl md:text-2xl text-center"
    :style="{ background: card.backgroundColor, color: card.textColor }"
  >
    <p class="max-w-full break-words">
      {{ card.text }}
    </p>
  </div>
</template>

<script>
export default {
  name: "Card",

  props: { card: Object }
};
</script>
