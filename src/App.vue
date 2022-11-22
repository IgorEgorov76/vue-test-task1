<template>
  <div id="app">
    <div class="container">
      <div class="container_inner">
        <div class="title">
          Список пользователей
        </div>
        <div class="search-block">
          <div class="search-block__form">
            <form class="search-block__form-item">
              <img class="search-block__form-loupe" :src="require('./assets/svg/search-block__form-loupe.svg')">
              <input v-model="search" class="search-block__form-input" type="text" placeholder="Поиск по имени или e-mail">
            </form>
            <div class="search-block__form-filter">
              <img class="search-block__form-broom" src=".//assets/svg/search-block__form-broom.svg">
              <button @click="clearFilter" class="search-block__form-btn">Очистить фильтр</button>
            </div>
          </div>
        </div>
        <div class="sorting">
          <div class="sorting__text">Сортировка:</div>
          <button
              @click="sortingDataMethod"
              class="sorting__btn"
              :class="`sorting__btn_${sortingData}`"
          >
            Дата регистрации
          </button>
          <button
              @click="sortingRatingMethod"
              class="sorting__btn"
              :class="`sorting__btn_${sortingRating}`"
          >
            Рейтинг
          </button>
        </div>
        <div class="table-block">
          <table class="table-block__main">
            <tr class="table-block__tr">
              <td class="table-block__td">Имя пользователя</td>
              <td class="table-block__td">E-mail</td>
              <td class="table-block__td">Дата регистрации</td>
              <td class="table-block__td">Рейтинг</td>
            </tr>
            <tr class="table-block__tr" v-for="user in currentPageResult" :key="user.email">
              <td class="table-block__td">{{ user.username }}</td>
              <td class="table-block__td">{{ user.email }}</td>
              <td class="table-block__td">{{ user.normalizeDate }}</td>
              <td class="table-block__td_f">
                <div class="table-block__td-item">{{ user.rating }}</div>
                <button class="table-block__td-btn" @click="openModal(user.email)">
                  <img src=".//assets/svg/table-block__td-btn.svg">
                </button>
              </td>
            </tr>
          </table>
        </div>
        <div class="page-block">
          <div class="page-block__item" v-for="item in pageCount" @click="changePage(item)" :key="item">{{ item }}</div>
        </div>
        <v-popup
            :is-open="isPopupOpen"
            @ok="popupConfirmed"
            @close="isPopupOpen = false"
        >
        </v-popup>
      </div>
    </div>
  </div>
</template>

<script>
import format from 'date-fns/format'
import VPopup from "@/components/v-popup";
import axios from 'axios'


export default {
  name: 'App',
  components: { VPopup },
  data() {
    return {
      users: [],
      search: '',
      sortingRating: false,
      sortingData: false,
      page: 1,
      isPopupOpen: false,
      newIndex: ''
    }
  },

  methods: {
    clearFilter() {
      this.search = ''
      this.sortingRating = false
      this.sortingData = false
    },

    sortingRatingMethod() {
      this.sortingData = false
      if (this.sortingRating === 'desc') {
        this.sortingRating = 'asc'
      } else {
        this.sortingRating = 'desc'
      }
    },

    sortingDataMethod() {
      this.sortingRating = false
      if (this.sortingData === 'desc') {
        this.sortingData = 'asc'
      } else {
        this.sortingData = 'desc'
      }
    },

    changePage(item) {
      this.page = item
    },

    axiosDataUsers() {
      axios
          .get('https://5ebbb8e5f2cfeb001697d05c.mockapi.io/users')
          .then(response => {
            const data = response.data
            this.users = data.map(i => {
              const date = new Date(i.registration_date)
              i.timestamp = +date
              i.normalizeDate = format(date, 'dd.MM.yy')
              return i
            })
          })
    },

    popupConfirmed() {
      this.isPopupOpen = false
      this.users.splice(this.newIndex, 1)
    },

    openModal(email) {
      this.isPopupOpen = true
      this.newIndex = this.users.findIndex(el => el.email === email)
    }
  },

  computed: {
    filteredUsers() {
      let filteredAfterSearchArray = this.users.filter(i => {
        const email = i.email?.toLowerCase()
        const username = i.username?.toLowerCase()
        const search = this.search?.toLowerCase()

        return email.indexOf(search) !== -1 || username.indexOf(search) !== -1
      })

      if (this.sortingRating) {
        if (this.sortingRating === 'desc') {
          filteredAfterSearchArray = filteredAfterSearchArray.sort((a, b) => a.rating - b.rating)
        } else {
          filteredAfterSearchArray = filteredAfterSearchArray.sort((a, b) => b.rating - a.rating)
        }
      }

      if (this.sortingData) {
        if (this.sortingData === 'desc') {
          filteredAfterSearchArray = filteredAfterSearchArray.sort((a, b) => a.timestamp - b.timestamp)
        } else {
          filteredAfterSearchArray = filteredAfterSearchArray.sort((a, b) => b.timestamp - a.timestamp)
        }
      }

      return filteredAfterSearchArray
    },

    currentPageResult() {
      return this.filteredUsers.slice(this.page * 5 - 5, this.page * 5)
    },

    pageCount() {
      return Math.ceil(this.filteredUsers.length / 5)
    }
  },

  watch: {
    filteredUsers() {
      this.page = 1
    }
  },

  mounted() {
    this.axiosDataUsers()
  }
}


</script>


<style lang="scss">

body {
  margin: 0;
}

button {
  font-family: 'Inter', sans-serif;
  font-weight: 500;
}

.container {
  margin: auto;
  max-width: 1000px;
  background-color: #F7F7F7;
  font-family: 'Inter', sans-serif;
  font-weight: 500;

  &_inner {
    max-width: 961px;
    margin-left: 27px;
  }
}

.title {
  padding-top: 12px;
  margin-bottom: 20px;
  font-size: 24px;
  color: #333333;
  font-family: 'Inter', sans-serif;
  font-weight: 700;
}

.search-block {
  height: 102px;
  background-color: #FFFFFF;
  border-radius: 7px;
  box-shadow: 0 18px 15px 0 rgba(148, 148, 148, 0.15);
  margin-bottom: 72px;

  &__form {
    padding-top: 12px;
    padding-left: 16px;
    padding-right: 44px;
  }

  &__form-item {
    display: flex;
    border-radius: 4px;
    background-color: #ECEFF0;
    margin-bottom: 25px;
  }

  &__form-loupe {
    margin-left: 8px;
    margin-right: 8px;
  }

  &__form-input {
    height: 34px;
    border: none;
    color: #9EAAB4;
    font-size: 12px;
    background-color: #ECEFF0;
    min-width: 300px;
  }

  &__form-input:active, :hover, :focus {
    outline: none;
  }

  &__form-filter {
    display: flex;
  }

  &__form-btn {
    font-size: 12px;
    color: #4F4F4F;
    background-color: #FFFFFF;
    border: none;
  }

  &__form-btn:hover{
    cursor: pointer;
    color: #262626;
  }
}

.sorting {
  display: flex;
  margin-bottom: 15px;

  &__text {
    margin-top: auto;
    margin-bottom: auto;
    margin-right: 8px;
    font-size: 10px;
    color: #9EAAB4;
  }

  &__btn {
    margin-top: auto;
    margin-bottom: auto;
    margin-right: 8px;
    padding: 0;
    font-size: 10px;
    color: #9EAAB4;
    background: none;
    border: none;
    border-bottom: dashed 1px #9EAAB4;

    &_false {
      margin-right: 14px;
    }

    &_asc {
      margin-right: 8px;
      &::after {
        content: '\2191';
      }
    }

    &_desc {
      margin-right: 8px;
      &::after {
        content: '\2193';
      }
    }
  }

  &__btn:focus {
    color: #333333;
    border-bottom: dashed 1px #333333
  }
}

table {
  border-collapse: collapse;
  margin-bottom: 15px;
}

.table-block {
  background-color: #FFFFFF;
  padding-left: 16px;
  padding-right: 16px;
  border-radius: 7px;

  &__main {
    width: 100%;
  }

  &__tr {
    color: #9EAAB4;
    font-size: 10px;
    height: 50px;
    border-bottom: 1px solid #F7F7F7;
  }

  &__td {
    width: 25%;
    height: 52px;
    color: #9EAAB4;
    font-size: 10px;

    &_f {
      height: 52px;
      width: 170px;
      display: flex;
      justify-content: space-between;
    }
  }

  &__td-btn {
    width: 15px;
    border: none;
    background: none;
  }

  &__td-btn:hover {
    cursor: pointer;
  }

  &__td-item{
    margin-bottom: auto;
    margin-top: auto;
  }
}

.page-block {
  display: flex;
  justify-content: center;
  padding-bottom: 20px;

  &__item {
    font-size: 10px;
    color: #9EAAB4;
    cursor: pointer;
  }

  &__item:hover {
    color: #575d62;
  }

  &__item:not(:last-child) {
    margin-right: 5px;
  }
}

</style>
