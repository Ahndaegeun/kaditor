<template>
  <div class="main-content">
    <div class="text-editor-header">
      <button v-for="item in btnList" :key="item"
              type="button" 
              class="btn" 
              :data-element="item.ele"
              @click="btnClick(item.ele); getText()">
                <i :class="item.icon"></i>
              </button>
      
      <div class="file-box">
        <label for="upload-file">
          <i class="fa fa-file-image-o"></i>
        </label>
        <input id="upload-file" type="file" @change="uploadFile">
      </div>
      
      <input id="color" type="color" v-model="color">

      <input type="file" class="file" @change="uploadFile">
    </div>
    <div id="content" @keyup="getText" contenteditable="true"></div>
    <button type="button" class="export-btn" @click="exportContent">글 작성</button>
  </div>
</template>

<script>
export default {
  name: 'editor',
  data() {
    return {
      exportFile: '',
      exportC: '',
      content: '',
      image: '',
      color: '#000000',
      btnList: [
        {
          ele: 'bold',
          icon: 'fa fa-bold'
        },
        {
          ele: 'italic',
          icon: 'fa fa-italic'
        },
        {
          ele: 'underline',
          icon: 'fa fa-underline'
        },
        {
          ele: 'insertUnorderedList',
          icon: 'fa fa-list-ul'
        },
        {
          ele: 'insertOrderedList',
          icon: 'fa fa-list-ol'
        },
        {
          ele: 'createLink',
          icon: 'fa fa-link'
        },
        {
          ele: 'justifyLeft',
          icon: 'fa fa-align-left'
        },
        {
          ele: 'justifyCenter',
          icon: 'fa fa-align-center'
        },
        {
          ele: 'justifyRight',
          icon: 'fa fa-align-right'
        },
        {
          ele: 'justifyFull',
          icon: 'fa align-justify'
        },
      ]
    }
  },
  methods: {
    btnClick(command) {
      if(command === 'createLink') {
        const url = prompt('Enter thr link here: ', 'http://')
        document.execCommand(command, false, url);
      } else if(command === 'insertImage') {
        const img = `<img style="width: 300px; height: 300px;" src="data:image/*;base64,${this.image}" alt="img"/>`
        document.execCommand('insertHTML', false, img)
      }else if(command === 'foreColor') {
        document.execCommand('ForeColor', false, this.color)
      } else {
        document.execCommand(command, false, null)
      }
    },
    uploadFile(e) {
      const files = e.target.files
      const file = files[0]

      const maxSize = 5 * 1024 * 1024
      const fileSize = file.size

      if(fileSize > maxSize) {
        alert('첨부파일은 5MB 이내로 등록 가능합니다.')
        e.target.value = ''
        return
      }
      this.exportFile = files

      if(files && file){
        const reader = new FileReader()

        reader.onload = readerEvt => {
          const binaryString = readerEvt.target.result
          this.image = btoa(binaryString)
          this.btnClick('insertImage')
        }
        reader.readAsBinaryString(file)
      }
    },
    getText() {
      this.content = document.querySelector('#content').innerHTML
    },
    exportContent() {
      this.exportC = document.querySelector('#content').innerHTML
      const returnData = {
        _data: this.exportC,
        _file: this.exportFile
      }
      this.$emit('exportContent', returnData)
    }
  },
  watch: {
    color() {
      this.btnClick('foreColor')
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
}

body {
  font-family: sans-serif;
}

.main-content {
  width: 500px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  box-shadow: 0 10px 20px rgba(0, 0, 0, .19);
}

.text-editor-header {
  background: #ecf0f1;
  padding: 5px;
  display: flex;
  align-items: center;
}

.text-editor-header .btn {
  border: none;
  outline: none;  
  background: transparent;
  margin-right: 5px;
  cursor: pointer;
}

.file-box,
.color-box {
  display: inline-block;
  position: relative;
  margin-right: 5px;
}

#upload-file{
  display: none;
}

[type="color"] {
  border: none;
}

#content {
  min-height: 200px;
  border: 1px solid #ecf0f1;
  border-top: 0px;
  padding: 10px;
}

.file {
  margin-left: auto;
  width: 35%;
}

.export-btn {
  position: absolute;
  bottom: 10px;
  right: 10px;
  border: none;
  outline: none;
  background: #FF8906;
  color: #fff;
  cursor: pointer;
  padding: 5px 10px;
  border-radius: 10px;
}
</style>