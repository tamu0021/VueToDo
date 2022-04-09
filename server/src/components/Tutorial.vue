<template>
  <div>
    <label v-for="label in options" v-bind:key="label.value">
      <input type="radio" v-model="current" v-bind:value="label.value">{{ label.label }}
    </label>
    <table>
      <thead>
        <tr>
          <th class="id">ID</th>
          <th class="comment">コメント</th>
          <th class="state">状態</th>
          <th class="button">-</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in computedTodos" v-bind:key="item.id">
          <th>{{ item.id }}</th>
          <td>{{ item.comment }}</td>
          <td class="state">
            <button v-on:click="doChangeState(item)">{{ labels[item.state] }}</button>
          </td>
          <td class="button">
            <button v-on:click="doRemove(item)">削除</button>
          </td>
        </tr>
      </tbody>
    </table>
    <h2>新しい作業の追加</h2>
    <form class="add-form" v-on:submit.prevent="doAdd">
      コメント <input type="text" ref="comment">
      <button type="submit">追加</button>
    </form>
  </div>
</template>

<script>
import todoStorage from '../plugins/todoStorage'

export default {
  name: 'Tutorial',
  data () {
    return {
      todos: [],
      uid: 0,
      options: [
        { value: -1, label: 'すべて' },
        { value: 0, label: '作業中' },
        { value: 1, label: '完了' }
      ],
      current: -1
    }
  },
  computed: {
    labels () {
      return this.options.reduce(function (a, b) {
        return Object.assign(a, { [b.value]: b.label })
      }, {})
    },
    computedTodos: function () {
      return this.todos.filter(function (el) {
        return this.current < 0 ? true : this.current === el.state
      }, this)
    }
  },
  created () {
    this.todos = todoStorage.fetch()
    this.uid = this.todos.length
  },
  watch: {
    todos: {
      handler: function (todos) {
        todoStorage.save(todos)
      },
      deep: true
    }
  },
  methods: {
    doAdd: function (event, value) {
      var comment = this.$refs.comment
      if (!comment.value.length) {
        return
      }
      this.todos.push({
        id: this.uid++,
        comment: comment.value,
        state: 0
      })
      comment.value = ''
    },
    doChangeState: function (item) {
      item.state = item.state ? 0 : 1
    },
    doRemove: function (item) {
      const index = this.todos.indexOf(item)
      this.todos.splice(index, 1)
    }
  }
}
</script>

<style scoped>
</style>
