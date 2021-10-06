<template>
  <div v-if="people.length !== 0">
    <div class="block-user user-item" v-for="user in people" :key="user.id">
      <span> {{ user.firstName }}</span>
      <button class="btn red" @click="$emit('remove', user.id)">Удалить</button>
    </div>
  </div>
    <div class="block-user" v-else>
      <span> Пользователей пока нет </span>
      <button class="btn green" @click="$emit('load')">Попробовать загрузить пользователей</button>
    </div>
</template>

<script>
export default {
  emits:  ['load', 'remove'],
  props: ['people'],
  methods: {},
};
</script>
