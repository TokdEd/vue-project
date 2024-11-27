<template>
    <div class="main-content">
      <!-- 輸入名字 -->
      <div id="question" v-if="!username">
        <p id="questionText">{{ questionText }}</p>
        <input v-model="nameInput" id="nameInput" placeholder="Enter your name" />
        <button @click="submitName" id="submitName">Submit</button>
      </div>
  
      <!-- 顯示初始問題 -->
      <div id="typing" v-if="username && !selectedLanguage && showTyping" class="typing">{{ typingText }}</div>
  
      <!-- 語言選擇 -->
      <div id="buttons" v-if="showLanguageButtons">
        <button @click="selectLanguage('english')" id="englishButton">English</button>
        <button @click="selectLanguage('chinese')" id="chineseButton">中文</button>
      </div>
  
      <!-- 顯示興趣問題 -->
      <div id="interest" v-if="selectedLanguage && !isInterested">
        <p id="interestText">{{ interestQuestion }}</p>
        <button @click="handleInterestResponse(true)" id="yesButton">Yes</button>
        <button @click="handleInterestResponse(false)" id="noButton">No</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "Main",
    data() {
      return {
        nameInput: "",
        username: "",
        selectedLanguage: "",
        typingText: "",
        questionText: "What's your name?",
        interestQuestion: "",
        showTyping: false,
        showLanguageButtons: false,
        isInterested: null,
        initialText: "English or Chinese?",
        interestTextEnglish: "Are you interested in our products?",
        interestTextChinese: "你是否對我們的商品感興趣呢?",
        noInterestText: "Have a good day! :)",
      };
    },
    methods: {
      submitName() {
        if (this.nameInput.trim()) {
          this.username = this.nameInput.trim();
          this.typingText = this.initialText;
          this.showTyping = true;
          this.startTyping(() => {
            this.showTyping = false;
            this.showLanguageButtons = true;
          });
        }
      },
      selectLanguage(language) {
        this.selectedLanguage = language;
        this.interestQuestion =
          language === "english"
            ? this.interestTextEnglish
            : this.interestTextChinese;
        this.showLanguageButtons = false;
        this.showInterestQuestion();
      },
      handleInterestResponse(isInterested) {
        this.isInterested = isInterested;
        if (isInterested) {
          document.body.classList.add("fade-out");
          setTimeout(() => {
            this.$router.push("/main"); // 假設使用 Vue Router
          }, 1000);
        } else {
          this.typingText = this.noInterestText;
          this.showTyping = true;
          this.startTyping(null);
        }
      },
      startTyping(callback) {
        let index = 0;
        const text = this.typingText;
        this.typingText = ""; // 清空以顯示逐字效果
        const typing = () => {
          if (index < text.length) {
            this.typingText += text.charAt(index);
            index++;
            setTimeout(typing, 100);
          } else if (callback) {
            callback();
          }
        };
        typing();
      },
      showInterestQuestion() {
        this.showTyping = true;
        this.typingText = this.interestQuestion;
        this.startTyping(() => {
          this.showTyping = false;
        });
      },
    },
    mounted() {
      // 初始化時顯示輸入框
      this.questionText = "What's your name?";
    },
  };
  </script>
  
  <style>
  .typing {
    font-size: 1.2em;
    margin: 10px;
  }
  
  #buttons {
    display: flex;
    gap: 10px;
  }
  
  .hidden {
    display: none;
  }
  
  .fade-out {
    opacity: 0;
    transition: opacity 1s ease-in-out;
  }
  </style>