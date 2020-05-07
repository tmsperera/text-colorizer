<template>
  <div
    v-cloak
    class="flex flex-col items-center justify-center min-h-screen p-10"
  >
    <div class="flex flex-col w-full md:w-2/3">
      <h1 class="text-4xl font-hairline text-purple-600 text-center">
        Colorize your text
      </h1>

      <form @submit.prevent="addCard" class="mt-3 md:mt-4">
        <div class="w-full flex flex-col items-center justify-between">
          <input
            v-model="text"
            type="text"
            ref="text"
            autofocus
            autocomplete="off"
            class="w-full md:w-3/5 py-3 px-2 text-center rounded-lg hover:bg-gray-100 focus:outline-none focus:shadow-inner focus:bg-gray-100 placeholder-current focus:placeholder-gray-400 text-purple-600"
            :placeholder="
              cards.length
                ? 'Add your new text here to...'
                : 'Add your text here to...'
            "
          />

          <button
            type="submit"
            :disabled="!valid"
            :class="
              valid
                ? ['text-purple-600', 'shadow-md', 'border']
                : ['cursor-not-allowed', 'text-gray-500', 'border-none']
            "
            class="w-4/5 md:w-40 mt-3 md:mt-4 py-2 px-6 rounded-md focus:outline-none focus:border-purple-500 bg-white"
          >
            <div class="flex items-center justify-center">
              {{ loading ? "Colorizing..." : "Colorize" }}
            </div>
          </button>
        </div>
      </form>

      <div class="flex flex-col items-center justify-center mt-6">
        <card
          v-for="(card, index) in reversedCards"
          :key="index"
          :card="card"
          class="w-full md:w-auto max-w-full"
        ></card>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Card from "./components/Card";
import invert from "invert-color";

export default {
  name: "App",

  data: function() {
    return {
      text: "",
      cards: [],
      loading: false
    };
  },

  components: { Card },

  computed: {
    /**
     * Check if the inputs are valid.
     *
     * @returns {boolean}
     */
    valid() {
      return !!this.text.trim();
    },

    /**
     * Reverse ordered card list.
     *
     * @returns {*[]}
     */
    reversedCards() {
      return [...this.cards].reverse();
    }
  },

  methods: {
    /**
     * Adds new card.
     *
     * @returns {Promise<void>}
     */
    async addCard() {
      if (this.loading) {
        return;
      }

      this.loading = true;

      this.cards.push(await this.createCard(this.text));

      this.updateTitle(this.text);

      this.reset();

      this.loading = false;
    },

    /**
     * Creates a new card.
     *
     * @param text
     * @returns {Promise<{backgroundColor: string, text: (*|string), textColor: string}>}
     */
    async createCard(text) {
      let backgroundColor = "#6d4298";
      let textColor = "#FFFFFF";

      try {
        const { data: fetched } = await this.fetchColor();

        backgroundColor = fetched.color;
        textColor = invert(fetched.color);
      } catch (e) {
        console.log(
          "Colorizing server did not responded. Colors set to defaults."
        );
      }

      return {
        text: text.trim(),
        textColor: textColor,
        backgroundColor: backgroundColor
      };
    },

    /**
     * Updates the document title with given text.
     * @param text
     */
    updateTitle(text) {
      document.title = text;
    },

    /**
     * Fetch the color form the api end-point.
     *
     * @returns {Promise<AxiosResponse<any>>}
     */
    fetchColor() {
      return axios.get("http://api.creativehandles.com/getRandomColor");
    },

    /**
     * Resets the input field and re focus it.
     */
    reset() {
      this.text = "";
      this.$refs.text.focus();
    }
  }
};
</script>

<style lang="scss">
/**
Hide un-compiled mustache bindings until the Vue instance is ready
*/
[v-cloak] {
  display: none;
}

@import "assets/scss/main";
</style>
