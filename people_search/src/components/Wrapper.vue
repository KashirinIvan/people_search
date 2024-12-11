<script>
export default {
  data() {
    return {
      isSearch: true,
      isJson: false,
      isInfo: true,
      index: '',
      avatar: "/Rectangle.png",
      image: "/Rectangle1.png",
      about: "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.",
      cardList: [],
    }
  },
  methods: {
    async submit() {
      this.cardList = [];
      let inputSearch = document.getElementById('search').value;
      if (inputSearch !== '') {
        this.isSearch = false;
        let inputSearchList = inputSearch.replaceAll(' ', '').split(",");
        let resault = '';
        let parameter = '';
        if (/^\d+$/.test(inputSearchList[0])) {
          parameter = 'id='
        }
        else {
          parameter = 'username='
        }
        inputSearchList.forEach(function (element, index) {
          resault += parameter + element;
          if (index < inputSearchList.length - 1) {
            resault += '&'
          }
        });
        const response = await fetch('https://jsonplaceholder.typicode.com/users?' + resault)
        this.cardList = await response.json();
        if (this.cardList.length === 0) {
          this.isJson = true;
          this.isInfo = true;
        }
        else {
          this.isJson = false;
          this.isInfo = true;
          for (let i = 0; i < this.cardList.length; i++) {
            this.cardList[i].isVisible = true;
          }
        }
      }
      else {
        this.isSearch = true;
        this.isInfo = true;
        this.index = '';
      }
    },
    select: function (event) {
      this.isInfo = false;
      let target = event.currentTarget;
      this.index = target.id;
      let parent = document.querySelector('.scroll');
      let cardItem = parent.querySelectorAll('.card');
      for (let i = 0; i < cardItem.length; i++) {
        cardItem[i].classList.remove('active');
      }
      target.classList.add('active');
    }
  }
}
</script>

<template>
   <div class="wrapper">
    <h1 class="header-wrapper">Жилфонд</h1>
    <div class="main-window">
      <div class="sidebar">
        <form @submit="submit" onsubmit="return false">
          <div class="form-search">
            <label for="search" class="font-search-lable">Поиск сотрудника</label>
            <input class="search" id="search" type="text" placeholder="Введите Id или имя">
          </div>
        </form>
        <p class="title-resault">Результаты</p>
        <p v-if="isSearch" class="title-resault-info">Начните поиск</p>
        <p v-else-if="isJson" class="title-resault-info">Ничего не найдено</p>
        <div v-else class="scroll" id="scroll">
          <div v-for="(card, index) in cardList" class="card" v-on:click="select($event)" :key="index" :id="index">
            <div v-if="card.isVisible" class="card-avatar">
              <img class="image" :src="avatar" alt="Аватарка сотрудника" />
            </div>
            <div v-if="card.isVisible" id="card-info" class="card-info">
              <p class="card-info-name">{{ card.username }}</p>
              <p class="card-info-email">{{ card.email }}</p>
            </div>
          </div>
        </div>
      </div>
      <div class="info">
        <p v-if="isInfo" class="title-info">Выберите сотрудника, чтобы посмотреть его профиль</p>
        <div v-else class="info-card">
          <div class="info-card-image">
            <img :src="image" alt="Фото сотрудника" />
          </div>
          <div class="info-card-description">
            <p class="info-card-description-name">{{ cardList[index].name }}</p>
            <p class="info-card-description-email">email:<span class="whitespace">&nbsp</span><span class="email">{{
              cardList[index].email }}</span></p>
            <p class="info-card-description-phone">phone:<span class="whitespace">&nbsp</span><span class="phone">{{
              cardList[index].phone }}</span></p>
            <p class="info-card-description-about-title">О себе</p>
            <p class="info-card-description-about">{{ about }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
