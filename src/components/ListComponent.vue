<template>
  <div id="listComponent">
    <div v-for="(s, i) in sentences" v-bind:key="i" >
      <list-item-component :sentence="s" :index="i" ></list-item-component>
    </div>
  </div>    
</template>

<script>
import ListItemComponent from "../components/ListItemComponent.vue";

export default {
  name: "ListComponent",
  components: {
    ListItemComponent
  },
  props: {
    sentences: Array,
  },
  data() {
    return {
      // index: null,
    };
  },
  methods: {

      addItem(sentence) {
        console.log(sentence);
      },
      removeItem(sentenceIndex) {
        this.$emit('removeItem', sentenceIndex)
      }

  },
};
</script>

<style scoped>
#listComponent {
  width:100%;
  display: flex;
  flex-direction: column;
  align-content: center;
  justify-content: center;
}
</style>
