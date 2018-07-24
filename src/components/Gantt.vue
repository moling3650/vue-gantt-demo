<template>
  <div ref="gantt"></div>
</template>

<script>
import 'dhtmlx-gantt'

export default {
  name: 'Gantt',
  props: {
    tasks: {
      type: Object,
      default () {
        return {
          data: [],
          links: []
        }
      }
    }
  },
  methods: {
    $_initGanttEvents () {
      if (window.gantt.$_eventsInitialized) {
        return
      }
      window.gantt.attachEvent('onTaskSelected', (id) => {
        let task = window.gantt.getTask(id)
        this.$emit('task-selected', task)
      })
      window.gantt.attachEvent('onAfterTaskAdd', (id, task) => {
        this.$emit('task-updated', id, 'inserted', task)
        task.progress = task.progress || 0
        if (window.gantt.getSelectedId() === id) {
          this.$emit('task-selected', task)
        }
      })
      window.gantt.attachEvent('onAfterTaskUpdate', (id, task) => {
        this.$emit('task-updated', id, 'updated', task)
      })
      window.gantt.attachEvent('onAfterTaskDelete', (id) => {
        this.$emit('task-updated', id, 'deleted')
        if (!window.gantt.getSelectedId()) {
          this.$emit('task-selected', null)
        }
      })
      window.gantt.attachEvent('onAfterLinkAdd', (id, link) => {
        this.$emit('link-updated', id, 'inserted', link)
      })
      window.gantt.attachEvent('onAfterLinkUpdate', (id, link) => {
        this.$emit('link-updated', id, 'updated', link)
      })
      window.gantt.attachEvent('onAfterLinkDelete', (id, link) => {
        this.$emit('link-updated', id, 'deleted')
      })
      window.gantt.$_eventsInitialized = true
    }
  },
  mounted () {
    this.$_initGanttEvents()
    console.log(window.gantt)
    window.gantt.init(this.$refs.gantt)
    window.gantt.parse(this.$props.tasks)
  }
}
</script>

<style>
  @import 'dhtmlx-gantt/codebase/dhtmlxgantt.css'
</style>
