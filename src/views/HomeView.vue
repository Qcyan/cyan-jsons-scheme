<script setup lang="ts">
import { defineComponent, ref, Ref, reactive, watchEffect } from 'vue'
import { createUseStyles } from 'vue-jss'
import MonacoEditor from '@/components/MonacoEditor'

// schema
import demos from '@/demos'
// TODO: 在lib中export
type Schema = any
type UISchema = any

function toJson(data: any) {
  return JSON.stringify(data, null, 2)
}

// 样式
const useStyles = createUseStyles({
  container: {
    display: 'flex',
    flexDirection: 'column',
    height: '100%',
    width: '1200px',
    margin: '0 auto'
  },
  menu: {
    marginBottom: 20
  },
  code: {
    width: 700,
    flexShrink: 0
  },
  codePanel: {
    minHeight: 400,
    marginBottom: 20
  },
  uiAndValue: {
    display: 'flex',
    justifyContent: 'space-between',
    '& > *': {
      width: '46%'
    }
  },
  content: {
    display: 'flex'
  },
  form: {
    padding: '0 20px',
    flexGrow: 1
  },
  menuButton: {
    appearance: 'none',
    borderWidth: 0,
    backgroundColor: 'transparent',
    cursor: 'pointer',
    display: 'inline-block',
    padding: 15,
    borderRadius: 5,
    '&:hover': {
      background: '#efefef'
    }
  },
  menuSelected: {
    background: '#337ab7',
    color: '#fff',
    '&:hover': {
      background: '#337ab7'
    }
  }
})
// 变量
const classesRef = useStyles()
const classes = classesRef.value
const selected: Ref<number> = ref(0)
const methodRef: Ref<any> = ref()
const demo: {
  schema: Schema | null
  data: any
  uiSchema: UISchema | null
  schemaCode: string
  dataCode: string
  uiSchemaCode: string
} = reactive({
  schema: null,
  data: {},
  uiSchema: {},
  schemaCode: '',
  dataCode: '',
  uiSchemaCode: ''
})

watchEffect(() => {
  const index = selected.value
  const d = demos[index]
  demo.schema = d.schema
  demo.data = d.default
  demo.uiSchema = d.uiSchema
  demo.schemaCode = toJson(d.schema)
  demo.dataCode = toJson(d.default)
  demo.uiSchemaCode = toJson(d.uiSchema)
})
const handleChange = (v: any) => {
  demo.data = v
  demo.dataCode = toJson(v)
}

function handleCodeChange(filed: 'schema' | 'data' | 'uiSchema', value: string) {
  try {
    const json = JSON.parse(value)
    demo[filed] = json
    ;(demo as any)[`${filed}Code`] = value
  } catch (err) {
    // some thing
  }
}

const handleSchemaChange = (v: string) => handleCodeChange('schema', v)
const handleDataChange = (v: string) => handleCodeChange('data', v)
const handleUISchemaChange = (v: string) => handleCodeChange('uiSchema', v)
</script>

<template>
  <main>132</main>
  <div :class="classes.container">
    <div :class="classes.menu">
      <h1>Vue3 JsonSchema Form</h1>
      <div v-for="(demo, index) in demos" :key="index">
        <button
          :class="[classes.menuButton, selected === index ? classes.menuSelected : '']"
          @click="selected = index"
        >
          {{ demo.name }}
        </button>
      </div>
    </div>
    <div :class="classes.content">
      <div :class="classes.code">
        <MonacoEditor
          :code="demo.schemaCode"
          :class="classes.codePanel"
          @Change="handleSchemaChange"
          title="Schema"
        />
        <div :class="classes.uiAndValue">
          <MonacoEditor
            :code="demo.uiSchemaCode"
            :class="classes.codePanel"
            @Change="handleUISchemaChange"
            title="UISchema"
          />
          <MonacoEditor
            :code="demo.dataCode"
            :class="classes.codePanel"
            @Change="handleDataChange"
            title="Value"
          />
        </div>
      </div>
    </div>
  </div>
</template>
