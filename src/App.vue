<template>
  <div class="container">
    <app-alert :alert="alert" @close="alert = null"></app-alert>
    <div class="block-user">
      <form action="" class="block-user__form" @submit.prevent="postHandler">
        <div class="form-control">
          <label for="name">Имя пользователя</label>
          <input
            type="text"
            id="name"
            placeholder="Введите нового пользователя"
            v-model.trim="name"
          />
          <div>{{ this.name }}</div>
        </div>
        <button class="btn">Создать</button>
      </form>
    </div>
    <app-loader v-if="loading"></app-loader>
    <app-people-list
      v-else
      :people="people"
      @load="loadPeople"
      @remove="removePeople"
    >
    </app-people-list>
  </div>
</template>

<script>
import appPeopleList from "./components/appPeopleList.vue";
import appAlert from "./components/appAlert.vue";
import appLoader from "./components/appLoader.vue";
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      name: "",
      id: null,
      people: [],
      alert: null,
      loading: false,
    };
  },
  mounted() {
    this.loadPeople();
  },
  methods: {
    async postHandler() {
      await axios
        .post(
          "https://base-project-6bbd5-default-rtdb.europe-west1.firebasedatabase.app/users.json",
          {
            headers: {
              "Content-Type": "application/json",
            },
            firstName: this.name,
          }
        )
        .then(() => {
          this.people.push({
            firstName: this.name,
            id: this.id,
          });
        })
        .catch((err) => {
          console.log("Ошибка " + err);
        });
      this.name = "";
    },
    async loadPeople() {
      this.loading = true;
      setTimeout(async () => {
        try {
          const { data } = await axios.get(
            "https://base-project-6bbd5-default-rtdb.europe-west1.firebasedatabase.app/users.json"
          );
          this.people = Object.keys(data).map((key) => {
            return {
              id: key,
              firstName: data[key].firstName,
            };
          });
          this.loading = false;
        } catch (err) {
          this.alert = {
            type: "red",
            title: "Ошибка",
            text: "Список пользователей пуст",
          };
          this.loading = false;
        }
      }, 1000);
    },
    async removePeople(id) {
      try {
        const name = this.people.find((user) => user.id === id).firstName;
        await axios.delete(
          `https://base-project-6bbd5-default-rtdb.europe-west1.firebasedatabase.app/users/${id}.json`
        );
        this.people = this.people.filter((user) => user.id !== id);
        this.alert = {
          type: "green",
          title: "Успешно",
          text: `Пользователь "${name}" был удален`,
        };
      } catch (err) {
        console.log(err);
      }
    },
  },
  components: {
    "app-people-list": appPeopleList,
    "app-alert": appAlert,
    "app-loader": appLoader,
  },
};
</script>

<style lang="scss">
@import "./style/main.scss";
</style>
