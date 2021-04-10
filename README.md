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
# Events
### `@onChange({data,channel})`
- Fires every data change

`channel` - passes cell related methods
- `channel.[field item].disableCells(<String:[]>)`
- `channel.[field item].enableCells(<String:[]>)`
- `channel.[field item].lockRows(<String:[]>)`
- `channel.[field item].unlockRows(<String:[]>)`
