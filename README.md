A reactive marking table base on vue, this component ca be use in nuxt.js and will works ina  plane vue component, just clone and import then register as a component.
# Demo
![alt preview](preview.gif)

# Usage
```html
<template>
    <main>
        <channelEncoder 
            @onChange="changeHanlder"
            @onMount="mountHandler"
            :data="myData"
            :config="myConfig"
        />
    </main>
</template>

<script>
export default {
    data: () => ({
        myData: { // default values
            
        },
        myConfig: {
            fields: ['X Position', 'Y Position', 'Size', 'Luminance'], // the object keys, array of strings
            options: ['Categorical', 'Orderred', 'Quantitative'], // the possible value of those keys, array of strings or number
            maxWidth: '200px', // default 300px
            borderColor: 'red',
            textColor: 'green',
            backgroundColor: '#333',
            hoveredBackground: 'green'
        }
    }),
    methods: {
        changeHanlder({data,channel}) {
            // fires every data change
        },
        mountHandler({channel}) {
            
        }
    }
}
</script>
```

# Props
- `data` - the default state of the component
- `config` - configuration data of props

# Config prop
- **fields** | `Array` - an array of strings that would be the keys of the final object 
- **options** | `Array` - an array of strings that would be the possible values of the final object

- **maxWidth** | `String` - ex: `'200px'`

- **borderColor** | `String` - ex: `'gray'`

- **textColor** | `String` - ex: `'#333'`

- **backgroundColor** | `String` - ex: `'#333'`

- **hoveredBackground** | `String` - ex: `'#333'`

- **lockFields** | Array - ex: `['field1','field2']`

- **lockOptions** | Array - ex: `['Option1','Option2']`
# Events
### `@onChange({data,channel})`
Executes every data change

**`data`** - the latest data object

**`channel`** - contains cell manipulation functions
- `channel.[field item].disableCells(<Array of String>)` 
    - **input**: Array of option item names
    - **default**: null
    - **description**: disables the cell, makes the cell unclickable
- `channel.[field item].enableCells(<Array of String>)` 
    - **input**: Array of option item names
    - **default**: null
    - **description**: enable's the cell, makes the cell clickable
- `channel.[field item].lock(void>)`
    - **input**: Array of option item names
    - **default**: null
    - **description**: Makes the row of cells unclickable
- `channel.[field item].unlock(void)`
    - **input**: Array of option item names
    - **default**: null
    - **description**: Makes the row of cells clickable
- `channel.changeOperationType(<String>)`
    - **input**: operation name
    - **default**: "rw"
    - **options**: `r` & `rw`
    - **description**: if set to r, the encoder will be in `read only` mode, if set to `rw` the encoder will be set to `write and read` and the cells will be clickable to change the values.
- `channel.lockFieldItems(<Array of String>)`
    - **input**: Array of option item names
    - **default**: null
    - **description**: Makes the specified rows unclickable,
- `channel.unlockFieldItems(<Array of String>)`
    - **input**: Array of option item names
    - **default**: null
    - **description**: Makes the specified rows clickable,
- `channel.lockOptionItems(<Array of String>)` TODO
- `channel.unlockOptionItems(<Array of String>)` TODO
- `channel.addOptions(<Array of String>)`
    - **input**: Array of option item names
    - **default**: null
    - **description**: Creates new option column
- `channel.removeOptions(<Array of String>)`
    - **input**: Array of option item names
    - **default**: null
    - **description**: Removes an option column
- `channel.addFields(<Array of String>)`
    - **input**: Array of field item names
    - **default**: null
    - **description**: Create new field items
- `channel.removeFields(<Array of String>)`
    - **input**: Array of field item names
    - **default**: null
    - **description**: Remove field items
- `channel.getLatestConfig(void)`
    - **input**: void
    - **default**: null
    - **description**: Reuturns the latest config

### `@onMount({channel})`
Executes on component mount
