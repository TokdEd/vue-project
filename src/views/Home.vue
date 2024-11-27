<template>
  <div class="container">
    <!-- 名字輸入部分 -->
    <div v-if="showNameInput" id="question" class="fade-in">
      <div id="questionText" class="fade-in">{{ currentText }}</div>
      <input 
        type="text" 
        v-model="inputName" 
        placeholder="Enter your name" 
      />
      <button @click="submitName">Submit</button>
    </div>

    <!-- 打字效果部分 -->
    <div v-if="showTyping" id="typing" class="fade-in">
      {{ typedText }}
    </div>

    <!-- 語言選擇按鈕 -->
    <div v-if="showLanguageButtons" id="buttons" class="fade-in">
      <button @click="selectLanguage('english')">English</button>
      <button @click="selectLanguage('chinese')">Chinese</button>
    </div>

    <!-- 興趣詢問部分 -->
    <div v-if="showInterest" id="interest" class="fade-in">
      <div id="interestText" class="fade-in">{{ interestText }}</div>
      <div id="interestButtons">
        <button @click="handleInterest(true)">Yes</button>
        <button @click="handleInterest(false)">No</button>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex'

export default {
  name: 'Home',
  data() {
    return {
      inputName: '',
      typedText: '',
      currentText: "What's your name?",
      showNameInput: true,
      showTyping: false,
      showLanguageButtons: false,
      showInterest: false,
      typingIndex: 0
    }
  },
  computed: {
    ...mapState(['texts']),
    interestText() {
      return this.$store.state.selectedLanguage === 'english' 
        ? this.texts.interestTextEnglish 
        : this.texts.interestTextChinese
    }
  },
  methods: {
    typeWriter(text, callback) {
      this.typingIndex = 0
      this.typedText = ''
      const typing = () => {
        if (this.typingIndex < text.length) {
          this.typedText += text.charAt(this.typingIndex)
          this.typingIndex++
          setTimeout(typing, 100)
        } else if (callback) {
          callback()
        }
      }
      typing()
    },
    submitName() {
      if (this.inputName.trim()) {
        this.$store.commit('setUsername', this.inputName.trim())
        this.showNameInput = false
        this.showTyping = true
        this.typeWriter(this.texts.initialText, () => {
          this.showLanguageButtons = true
        })
      }
    },
    selectLanguage(language) {
      this.$store.commit('setLanguage', language)
      this.showLanguageButtons = false
      this.showTyping = false
      this.showInterest = true
    },
    handleInterest(isInterested) {
      if (isInterested) {
        document.body.classList.add('fade-out')
        setTimeout(() => {
          this.$router.push('/main')
        }, 1000)
      } else {
        this.showInterest = false
        this.showTyping = true
        this.typeWriter(this.texts.noInterestText)
      }
    }
  }
}
</script>

<style scoped>
/* 將原有的 CSS 樣式複製過來，但只保留這個組件需要的部分 */
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #000;
  color: #00FF00;
  font-family: 'Courier New', Courier, monospace;
  font-size: 2.5vw;
  padding: 20px;
  text-align: center;
}

/* 其他相關樣式... */
</style> 