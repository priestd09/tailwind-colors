<template>
  <dropdown
    trigger-css-class="inline-flex items-center focus:outline-none focus:shadow-outline mr-2"
    :close-when-clicked="true"
  >
    <template
      slot="dropdown-trigger"
    >
      Load
    </template>
    <template slot="dropdown-content">
      <ul class="bg-white rounded shadow">
        <li>
          <button
            @click="$emit('add-config', createColorArray(defaultConfig))"
            class="flex focus:outline-none hover:bg-gray-200 py-2 px-4 w-full"
          >
            Default Config
          </button>
        </li>
        <li>
          <button
            class="flex focus:outline-none hover:bg-gray-200 py-2 px-4 w-full"
            @click="$refs.selectConfig.click()"
          >
            Import tailwind config
          </button>
        </li>
      </ul>
      <input
          hidden
          ref="selectConfig"
          type="file"
          accept="text/javascript"
          @change="handleSelectLocalConfig"
      >
    </template>
  </dropdown>
</template>

<script>
import Dropdown from '@/components/Dropdown'
import defaultConfig from '@/data/defaultConfig'

function createColorArray (colors) {
  const inputColors = []
  for (const color in colors) {
    if (typeof colors[color] === 'object') {
      const shades = []
      for (const shade in colors[color]) {
        shades.push({ name: isNaN(shade) ? shade : Number(shade), hexCode: colors[color][shade] })
      }
      inputColors.push({ name: color, shades })
    } else {
      inputColors.push({ name: color, shades: [{ name: '', hexCode: colors[color] }] })
    }
  }
  return inputColors
}

export default {
  components: {
    Dropdown
  },
  data () {
    return {
      defaultConfig,
      loadedConfig: {}
    }
  },
  methods: {
    handleSelectLocalConfig () {
      const file = event.target.files[0]
      const fileReader = new FileReader()
      fileReader.onload = (event) => {
        let config = fileReader.result.toString()
        config = config.replace(/require\(.+?\)/g, '')
        /* eslint no-eval: 0 */
        config = eval(config)
        const colorConfig = createColorArray(config.theme.hasOwnProperty('extend') ? config.theme.extend.colors : config.theme.colors)
        this.$emit('add-config', colorConfig)
      }
      fileReader.readAsText(file)
    },
    createColorArray
  }
}
</script>
