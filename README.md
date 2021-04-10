A reactive marking table base on vue, this component ca be used, in nuxt and in plane vue component
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
- `channel.addOption(<Array of String>)` TODO
- `channel.removeOption(<Array of String>)` TODO
- `channel.addFields(<Array of String>)` TODO
- `channel.removeFields(<Array of String>)` TODO
- `channel.getLatestConfig(void)` TODO
- `channel.lockFieldItems(<Array of String>)`
- `channel.unlockFieldItems(<Array of String>)`
### `@onMount({channel})`
Executes on component mount
