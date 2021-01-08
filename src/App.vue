<template>
<div>
    <!-- <input  type="file" id="myFile" name="filename" /> -->
    <div id = "settings" class = "settings"> 
      <div id = "spaceSelectorDiv">
          <button class = "languageType" @click="changeMaxType('spaces')">Spaces</button>
          <button class = "languageType" @click="changeMaxType('chars')">Characters</button>
      </div>

      <div id = "wordCountWrapper" class = "settings">
        <div>
          <label v-if="languageType == 'spaces'">Min word count:</label>
          <label v-if="languageType == 'chars'">Min char count:</label>

          <input type="number" id="minCount" min="1" value = "3" max="100">
        </div>
        <div>
          <label v-if="languageType == 'spaces'">Max word count:</label>
          <label v-if="languageType == 'chars'">Max char count:</label>
          <input type="number" id="maxCount" min="1" value = "10" max="100">
        </div>
      </div>

      <div id="charWrapper" class = "settings">
        <div>
          <label>Sentence ending characters:</label>
          <input type="text" id="splitChars" size="10" value = ".!?">
        </div>        
        <div id = "languageButtonDiv">
          <button class = "languageButton" @click="changeLanguage('english')">English</button>
          <button class = "languageButton" @click="changeLanguage('japanese')">Japanese</button>
        </div>
      </div>
      <div id = "fileButtonDiv" class = "settings">
          <button @click="clickUpload" id ="fileButton">Upload File</button>
          <input @change="loadFile($event)" type="file" id="fileInput">
      </div>
      <div id="downloadWrapper" class = "settings">
        <button id="downloadButton" @click="createAndDownload">Download Sentences</button>
      </div>
    </div>

    <div id = "listDiv">
      <list-component :sentences="sentences" @removeItem="removeSentence($event)"></list-component>
    </div>
</div>
</template>

<script>
// import * as pdfjsLib from 'pdfjs-dist';
import ListComponent from "./components/ListComponent.vue";

export default {
  name: 'App',
  components: {
    ListComponent
  },
  data() {
    return {
      file: null,
      downloadText: "",
      sentences: null,
      languageType: "spaces",
    };
  },
  methods: {
    loadFile(event) {
      //reset values incase they upload a different file
      this.file = null;
      this.downloadText = "";
      this.sentences = null;

      //get the file data 
      let fileData =  event.target;
      let fileSplit = fileData.files[0].name.split('.');
      let fileExt = fileSplit[fileSplit.length - 1];
      console.log(fileExt);
      this.file = fileData.files[0];
      console.log(this.file);
      switch(fileExt) {
        case("txt"):
        console.log("txt file");

        this.getTxtText(this.file);
        break;
        case("pdf"):

        break;
        case("epub"):
        
        break;
      }

    },
    changeLanguage (language) {
      switch(language) {
        case("english"):
        document.getElementById("splitChars").value = ".!?"
        break;
        case("japanese"):
        document.getElementById("splitChars").value = "。！？"
        break;
      }

    },
    changeMaxType(newType) {
      this.languageType = newType;
      if(newType == "spaces") {
        document.getElementById("minCount").value = 3;
        document.getElementById("maxCount").value = 10;
      }
      else if (newType == "chars") {
        document.getElementById("minCount").value = 5;
        document.getElementById("maxCount").value = 25;       
      }
    },
    // async loadPDF(file) {
      
    // },
    // async getPdfText(data) {

    // },

    readTxtFile(event) {
      // console.log(event.target.result);
      console.log("processing text");
      this.processText(event.target.result);
    },
    getTxtText(file) {
      console.log("reading text");

      var reader = new FileReader();
      reader.addEventListener('load', this.readTxtFile);
      reader.readAsText(file);
    },
    processText(text) {
      let chars = document.getElementById("splitChars").value;
      let re = new RegExp("[^" + chars + "]+[" + chars + "]+", 'g');
      this.sentences = text.match( re );
      let min = document.getElementById("minCount").value;
      let max = document.getElementById("maxCount").value;

      console.log("trimming text");

      //trim out long sentences
      if(this.sentences === null) {
        alert("Issue getting sentences, assure the characters you are using match the language of the text.");  

      }
      else {
        console.log("sentence count pre trim ", this.sentences.length)
        for(let i = 0; i < this.sentences.length; i ++) {
          if(this.languageType == 'spaces') {
            let temp = this.sentences[i].split(" ");
            if(temp.length > max || temp.length < min) {
              this.sentences.splice(i, 1);
              i--;
            }
          } else if(this.languageType == 'chars') { // check if max chars are 
            if(this.sentences[i].length > max || this.sentences[i].length < min) {
              this.sentences.splice(i, 1);
              i--;
            }
          }

        }
        console.log("sentence count post trim ", this.sentences.length)
      }


    },
    createAndDownload() {
      if(this.sentences != null && this.sentences.length > 0) {
        //format the sentences for the file
        this.downloadText += "tags:bookScrape\n";
        for(let i = 0; i < this.sentences.length; i ++) {
          this.downloadText += this.sentences[i] + "|Backside\n";
        }

        //create text file and download element then download
        var element = document.createElement('a');
        element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(this.downloadText));
        element.setAttribute('download', "sentences");
        element.style.display = 'none';
        document.body.appendChild(element);

        element.click();
        document.body.removeChild(element);
      }
      else {
        alert("no sentences found");
      }
    },
    removeSentence: function(i) {
      console.log(i);
      this.sentences.splice(i, 1);
    },
    clickUpload() {
      document.getElementById("fileInput").click();
    }

  }
}
</script>


<style>
body {
  background-color: #f7f7f7;
}

div {
  /* margin-top: 10px; */
}
input[type=number]{
  width:40px;
  float: right; 
}
input[type=text]{
  width:100px;
}
/* input[type=file]{
  width:auto;
  height:100%;
} */
label {
  padding:5px;
}

#settings {
  display:flex;
  flex-direction: row;
  justify-content: center;
}
#settings > div {
  padding:10px;
}
#settings > div > div {
  margin-top:5px; 
}

#spaceSelectorDiv {
  display:flex;
  flex-direction: column;
  justify-content: center;
}
.languageType {
  width: 100%;
  height: 25px;
  /* padding:1px; */
  margin-top:1px;
  background-color: rgb(27, 25, 25);
  /* border-color: #000; */
  border: none;
  border-radius: 50px;
  color: #fff;
  cursor: pointer;
  outline: none;
}


#wordCountWrapper {
  display:flex;
  flex-direction: column;
  justify-content: center;
}

#languageButtonDiv {
  display: flex;
  flex-direction: row;
  justify-content: center;
}
.languageButton {
  width: 49%;
  height: 25px;
  /* padding:1px; */
  margin-left:1px;
  background-color: rgb(27, 25, 25);
  /* border-color: #000; */
  border: none;
  border-radius: 50px;
  color: #fff;
  cursor: pointer;
  outline: none;

}

#downloadButton {
  width:auto;
  height:100%;
  border: none;
  background-color: #cc4545;
  border-radius: 5px;
}

#fileInput {
  height: 0px; 
  width: 0px;
}
.fileUpload {
  border: 1px solid #ccc;
  display: inline-block;
  padding: 6px 12px;
  cursor: pointer;
}
#fileButton {
  width:100%;
  height:100%;
  background-color: #20c54a;
  border: none;
  border-radius: 5px;
}

#listDiv {
  width:80%;
  margin-left:10%;
  display:flex;
  flex-direction: row;
  align-content: center;
  justify-content: center;
}

</style>
