# Preview
![alt preview](preview.gif)

# Usage
```html
<template>
    <main>
        <channelEncoder 
            @onChange="changeHanlder"
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
        }
    }
}
</script>
```

# Props
- `data` - the default state of the component
- `config` - configuration data of props

# Config prop
- **fields** | `Array`


- **options** | `Array`

- **maxWidth** | `String`

- **borderColor** | `String`

- **textColor** | `String`

- **backgroundColor** | `String`

- **hoveredBackground** | `String`
# Events
- `@onChange({data,channel})`
    - fires every data change