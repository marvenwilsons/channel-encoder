<template>
    <!-- this component is useful when you have multiple fields with the same set of options  -->
    <main class="flex" >
        <section role="wrapper" id="wierd-table-inp" :style="{maxWidth, border: `1px solid ${borderColor}`, background: backgroundColor}" >
            <section style="z-index: -1" class="flex flexend" >
                <section   >
                    <div id="multi-opt" class="flex flexcol" >
                        <div v-for="(option,option_index) in options" :key="`${option}_${option_index}_${option_index * 2}`" 
                            :style="{borderRight: `1px solid ${borderColor}`, color: textColor}" 
                            class="opt-item" 
                            :id="option"
                            >
                            <small>{{option}}</small>
                        </div>
                    </div>
                </section>
            </section>
            <section style="z-index: 100;" class="overflowhidden" >
                <div :style="{borderTop: `1px solid ${borderColor}`, background: backgroundColor}" 
                    v-for="(field, field_index) in fields" :key="`${field}_${field_index}`" 
                    class="row" >
                    <!-- label -->
                    <div :id="field" class="label padleft050" :style="{color: textColor}" > <small>{{field}}</small> </div>
                    <!-- blocks -->
                    <div @click="disabledCells.includes(`${field}-${option}`) || operation == 'r' || getlockFields.includes(field) || getlockOptions.includes(option)  ? () => {} : registerSelection(field,option)"
                        @mouseenter="handleMouseActivity(true,`${field}-${option_index}`,`${option}-${field_index}`)"
                        @mouseleave="handleMouseActivity(false,`${field}-${option_index}`,`${option}-${field_index}`)"
                        v-for="(option,option_index) in options" 
                        :class="['ch-inp flex flexcenter', disabledCells.includes(`${field}-${option}`) || getlockFields.includes(field) || getlockOptions.includes(option) ? 'notallowed' : '']"
                        :style="{borderLeft: `1px solid ${borderColor}`}"
                        :key="`${option}_${option_index}`" 
                        :data-operation="operation"
                        :data-field="`${field}-${option_index}`"
                        :data-option="`${option}-${field_index}`"
                        :id="`${field}-${option}`"
                        >
                        <div v-if="ready" :style="{color: textColor}" >
                            <span v-if="value[field] == option" >
                                ✱
                            </span>
                        </div>
                    </div>
                </div>
            </section>
        </section>
    </main>
</template>

<script>
export default{
    props: ["data", "_key", "color", "key_index", "disabled", "config"],
    data: () => ({
        operation: 'rw',
        fields: undefined,
        options: undefined,
        value: {},
        ready: false,
        maxWidth: '300px',
        markedColor: 'gray',
        borderColor: 'lightgray',
        backgroundColor: 'white',
        textColor: '#333',
        hoveredBackground: 'rgba(211, 211, 211, 0.384)',
        disabledCells: [],
        lockFields: [],
        lockOptions: []
    }),
    computed: {
        getlockFields() {
            return this.lockFields
        },
        getlockOptions() {
            return this.lockOptions
        }
    },
    methods: {
        disableCell(key) {
            return (arrOfOptions) => {
                arrOfOptions.map(optionName => {
                    const cellId = key + '-' +optionName
                    const el = document.getElementById(cellId)
                    if(el) {
                        this.disabledCells.push(cellId)
                    } else {
                        alert(`disableCell Error: option named "${optionName}" does not exist found in arrOptions of disableCell method`)
                    }
                })
            }
        },
        enableCell(key) {
            return (arrOfOptions) => {
                arrOfOptions.map(optionName => {
                    const cellId = key + '-' +optionName
                    const indexOfElement = this.disabledCells.indexOf(cellId)

                    if(indexOfElement != -1) {
                        this.disabledCells.splice(indexOfElement,1)
                    }
                })
            }
        },
        changeOperationType(operation) {
            const validOperations = ['r','rw']
            if(validOperations.includes(operation)) {
                this.operation = operation
            } else {
                alert(`ChannelEncoder: Invalid Opeartion name ${operation}`)
            }
        },
        lock(key) {
            return () => {
                if(this.lockFields.includes(key) == false) {
                    this.lockFields.push(key)
                }
            }
        },
        lockFieldItems(arr) {
            arr.map(e => {
                this.lock(e)()
            })
        },
        unlock(key) {
            return () => {
                if(this.lockFields.includes(key) == true) {
                    this.lockFields.splice(this.lockFields.indexOf(key),1)
                }
            }
        },
        unlockFieldItems(arr) {
            arr.map(e => {
                this.unlock(e)()
            })
        },
        handleMouseActivity(isHovered,fieldName,optionName) {
            const lightUpHorizontal = (el) => {
                const selectedFieldElements = document.querySelectorAll(`[data-field='${el}']`)

                if(isHovered) {
                    // apply vertical style hover
                    const fId = fieldName.split('-')[0]
                    const OId = optionName.split('-')[0]
                    document.getElementById(fId).style.background = this.hoveredBackground
                    document.getElementById(OId).style.background = this.hoveredBackground
                    for(let i = 0; i < selectedFieldElements.length; i++) {
                        selectedFieldElements[i].style.background = this.hoveredBackground
                    }

                } else {
                    const fId = fieldName.split('-')[0]
                    const OId = optionName.split('-')[0]
                    document.getElementById(OId).style.background = 'none'
                    document.getElementById(fId).style.background = 'none'
                    for(let i = 0; i < selectedFieldElements.length; i++) {
                        selectedFieldElements[i].style.background = 'none'
                    }
                }
            }

            const lightUpVerctical = (el) => {
                const selectedFieldElements = document.querySelectorAll(`[data-option='${el}']`)

                if(isHovered) {
                    // apply vertical style hover
                    for(let i = 0; i < selectedFieldElements.length; i++) {
                        selectedFieldElements[i].style.background = this.hoveredBackground
                    }
                } else {
                    for(let i = 0; i < selectedFieldElements.length; i++) {
                        selectedFieldElements[i].style.background = 'none'
                    }
                }
            }

            //
            const max = parseInt(fieldName.split('-')[fieldName.split('-').length - 1])
            for(let i = max; i >= 0; i--) {
                const fields = fieldName.split('-')[0]
                lightUpHorizontal(`${fields}-${i}`)
            }

            const optionMax = parseInt(optionName.split('-')[optionName.split('-').length - 1])
            for(let i = optionMax; i >= 0; i--) {
                const option = optionName.split('-')[0]
                lightUpVerctical(`${option}-${i}`)
            }
        },
        channel() {
            const v = {}
            this.fields.map(e => {
                v[e] = {
                    disableCells: this.disableCell(e),
                    enableCells: this.enableCell(e),
                    lock: this.lock(e),
                    unlock: this.unlock(e)
                }
            })

            v.changeOperationType = this.changeOperationType
            v.lockFieldItems = this.lockFieldItems
            v.unlockFieldItems = this.unlockFieldItems
            return v
        },
        registerSelection(fieldName,optionName) {
            if(this.value[fieldName] != optionName) {
                this.value[fieldName] = optionName
            } else {
                this.value[fieldName] = undefined
            }
            
            this.ready = false

            setTimeout(() => {
                this.$emit('onChange',{data: this.value, channel: this.channel()})
                this.ready = true
            }, 1);
        }
    },
    mounted() {
        this.ready = false

        if(this.config) {
            this.maxWidth = this.config.maxWidth
            this.config.fields  && (this.fields = this.config.fields)
            this.config.options && (this.options = this.config.options)
            this.config.borderColor && (this.borderColor = this.config.borderColor)
            this.config.textColor && (this.textColor = this.config.textColor)
            this.config.backgroundColor && (this.backgroundColor = this.config.backgroundColor)
            this.config.hoveredBackground && (this.hoveredBackground = this.config.hoveredBackground)
            this.config.operation && (this.operation = this.config.operation)
            this.config.lockFields && (this.lockFields = this.config.lockFields)
            this.config.lockOptions && (this.lockOptions = this.config.lockOptions)
        }
        
        this.fields.map(e => {
            this.value[e] = undefined
        })

        if(this.data) {
            this.ready = true
            this.value = this.data
            this.$emit('onMount', { 
                channel: {
                    changeOperationType: this.channel().changeOperationType,
                    lockFieldItems: this.channel().lockFieldItems,
                    unlockFieldItems: this.channel().unlockFieldItems
                }
            })
        }
    }
}
</script>

<style>
.row {
    display: flex;
    z-index: 5;
    background: white;
    overflow: hidden !important;
}
.ch-inp {
    min-height:30px;
    min-width:30px;
}
[data-operation="rw"]:hover {
    cursor: pointer;
}
.label {
    flex: 1;
    display: flex;
    align-items: center;
    padding-right: 5px;
}
.label > * {
    width: 100%;
    text-align: right;
    
}
#multi-opt {
    writing-mode: vertical-rl;
    height: 100px !important;
    width: 100%;
    flex-direction: column-reverse;
}
.opt-item {
    transform: rotate(-180deg);
    width: 30px !important;
    text-align: left;
    padding-top: 5px;
    writing-mode: horizontal-lr
}
.notallowed {
    cursor: not-allowed !important;
    background: rgba(161, 144, 144, 0.055) !important;
    color: rgba(161, 144, 144, 0.055) !important;
}
.notallowed > div > span {
    color: rgba(161, 144, 144, 0.356) !important;
}
</style>
