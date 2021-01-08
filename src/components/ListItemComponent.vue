<template>
  <div id="item" v-bind:class="[ (index%2 === 0) ? 'even' : 'odd']">
    <div id = "sentenceWrapper">
      <p>{{sentence}}</p>
    </div>
    <div id = "removeButton">      
      <button id ="s" @click="removeItem">X</button>
    </div>   
  </div>    
</template>

<script>
export default {
  name: "ListItemComponent",
  props: {
    sentence: String,
    index: Number,
  },
  data() {
    return {

    };
  },
  computed: {
    css() {
      if(this.index%2) {
        document.getElementById("item").classList.add("even");
        console.log(document.getElementById("item").style.backgroundColor + this.sentence)
      }
      else {
        document.getElementById("item").classList.add("odd");
        console.log(document.getElementById("item").style.backgroundColor)

      }
      console.log(this.index%2);
      return(1);
    }

  },
  methods: {
      removeItem() {
        this.$parent.removeItem(this.index);
      },
      getClass() {
        // if(this.index%2) {
        //   return("#525493");
        // }
        // else {
        //   return("#ffffff");
        // }
      }
  },
};
</script>

<style scoped>
button {
  width:100%;
  height:100%;
  background-color: rgb(102, 102, 102);
  border: none;
  color: rgb(255, 255, 255);
}

button :active {
  border: none;
}
button:focus {outline:0;}

p {
  padding-left: 5px;
}
#item {
  width: auto; 
  display: flex;
  /* background-color: rgb(196, 196, 196); */
  border: 1px solid black;
}
.even {
  background-color: #d8d8d8;
}
.odd {
  background-color: #b4b4b4;
}
#sentenceWrapper { 
  width: 100%;
  height:100%;
}
#removeButton {
  display: flex;
  justify-content: flex-end;
  align-items: flex-end;
  width:auto;
  height: 100%;
}
</style>
