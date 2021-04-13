<template>
    <!-- this component is useful when you have multiple fields with the same set of options  -->
    <main v-if="mount" class="flex borderRad4 overflowhidden" :style="{border: `1px solid ${borderColor}`}" >
        <section role="wrapper" id="wierd-table-inp" :style="{maxWidth, background: backgroundColor}" >
            <section style="z-index: -1" class="flex flexend" >
                <section   >
                    <div v-if="showOptions" id="multi-opt" class="flex flexcol" >
                        <div v-for="(option,option_index) in options" :key="`${option}_${option_index}_${option_index * 2}`" 
                            :style="{borderRight: `1px solid ${borderColor}`, color: textColor}" 
                            class="opt-item" 
                            :id="`${option}${sessionId}`"
                            >
                            <small>{{option}}</small>
                        </div>
                    </div>
                </section>
            </section>
            <section style="z-index: 100;" class="overflowhidden" >
                <div :style="{borderTop: `1px solid ${borderColor}`, background: backgroundColor, zIndex: '5'}" 
                    v-for="(field, field_index) in getFields" :key="`${field}_${field_index}`" 
                    class="flex overflowhidden" >
                    <!-- label -->
                    <div :id="`${field}${sessionId}`" class="label padleft050" :style="{color: textColor}" > <small>{{field}}</small> </div>
                    <!-- blocks -->
                    <div @click="disabledCells.includes(`${field}-${sessionId}-${option}`) || operation == 'r' || getlockFields.includes(field) || getlockOptions.includes(`${sessionId}${option}`)  ? () => {} : registerSelection(field,option)"
                        @mouseenter="handleMouseActivity(true,`${field}-${sessionId}-${option_index}`,`${option}-${sessionId}-${field_index}`)"
                        @mouseleave="handleMouseActivity(false,`${field}-${sessionId}-${option_index}`,`${option}-${sessionId}-${field_index}`)"
                        v-for="(option,option_index) in getOptions" 
                        :class="['ch-inp flex flexcenter', disabledCells.includes(`${field}-${sessionId}-${option}`) || getlockFields.includes(field) || getlockOptions.includes(`${sessionId}${option}`) ? 'notallowed' : '']"
                        :style="{borderLeft: `1px solid ${borderColor}`}"
                        :key="`${option}_${option_index}`" 
                        :data-operation="operation"
                        :data-field="`${field}-${sessionId}-${option_index}`"
                        :data-option="`${option}-${sessionId}-${field_index}`"
                        :id="`${field}-${sessionId}-${option}`"
                        >
                        <div v-if="ready && showOptions" :style="{color: textColor}" >
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
        maxWidth: undefined,
        markedColor: 'gray',
        borderColor: 'lightgray',
        backgroundColor: 'white',
        textColor: '#333',
        hoveredBackground: 'whitesmoke',
        disabledCells: [],
        lockFields: [],
        lockOptions: [],
        showOptions: true,
        showFields: true,
        sessionId: undefined,
        mount: false
    }),
    computed: {
        getlockFields() {
            return this.lockFields
        },
        getlockOptions() {
            return this.lockOptions
        },
        getOptions() {
            return this.options
        },
        getFields() {
            return this.fields
        }
    },
    methods: {
        randomString(length) {
            var result           = '';
            var characters       = 'abcdefghijklmnopqrstuvwxyz';
            var charactersLength = characters.length;
            for ( var i = 0; i < length; i++ ) {
            result += characters.charAt(Math.floor(Math.random() * charactersLength));
            }
            return result;
        },
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
                    try {
                        const fId = fieldName.split('-')[0] + this.sessionId // field id
                        const OId = optionName.split('-')[0] + this.sessionId// option id
                        document.getElementById(fId).style.background = this.hoveredBackground
                        document.getElementById(OId).style.background = this.hoveredBackground
                        for(let i = 0; i < selectedFieldElements.length; i++) {
                            selectedFieldElements[i].style.background = this.hoveredBackground
                        }
                    } catch{ /** usually the error comes from sudden change, no big deal */ }

                } else {
                    try {
                        const fId = fieldName.split('-')[0] + this.sessionId // field id
                        const OId = optionName.split('-')[0] + this.sessionId// option id
                        document.getElementById(OId).style.background = 'none'
                        document.getElementById(fId).style.background = 'none'
                        for(let i = 0; i < selectedFieldElements.length; i++) {
                            selectedFieldElements[i].style.background = 'none'
                        }
                    }catch{ /** usually the error comes from sudden change, no big deal */  }
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
                lightUpHorizontal(`${fields}-${this.sessionId}-${i}`)
            }

            const optionMax = parseInt(optionName.split('-')[optionName.split('-').length - 1])
            for(let i = optionMax; i >= 0; i--) {
                const option = optionName.split('-')[0]
                lightUpVerctical(`${option}-${this.sessionId}-${i}`)
            }
        },
        addOptions(arr) {
            this.showOptions = false
            arr.map(e => {
                const checkEl = this.options.includes(e)
                if(!checkEl) {
                    this.options.push(e)
                }
            })

            setTimeout(() => {
                this.showOptions = true
            }, 1);
        },
        addFieldItems(arr) {
            arr.map(e => {
                const checkEl = this.fields.includes(e)
                if(!checkEl) {
                    this.fields.push(e)
                }
            })
        },
        removeFields(arr) {
            arr.map(e => {
                if(this.fields.includes(e)) {
                    this.fields.splice(this.fields.indexOf(e),1)
                }
            })
        },
        removeOptions(arr) {
            this.showOptions = false

            arr.map(e => {
                if(this.options.includes(e)) {
                    this.options.splice(this.options.indexOf(e),1)
                }
            })

            setTimeout(() => {
                this.showOptions = true
            }, 1);
        },
        getLatestConfig() {
            return {
                fields: this.fields,
                options: this.options,
                maxWidth: this.maxWidth,
                borderColor: this.borderColor,
                textColor: this.textColor,
                backgroundColor: this.backgroundColor,
                hoveredBackground: this.hoveredBackground
            }
        },
        lockOptionItems(arr) {
            arr.map(e => {
                if(this.lockOptions.includes(e) == false) {
                    this.lockOptions.push(e)
                }
            })
        },
        unlockOptionItems(arr) {
            arr.map(e => {
                if(this.lockOptions.includes(e) == true) {
                    this.lockOptions.splice(this.lockOptions.indexOf(e),1)
                }
            })
        },
        channel() {
            const v = {}
            this.fields.map(e => {
                v[e] = {
                    disableCells: this.disableCell(e),
                    enableCells: this.enableCell(e),
                    lock: this.lock(e),
                    unlock: this.unlock(e),
                }
            })

            v.changeOperationType = this.changeOperationType
            v.lockFieldItems = this.lockFieldItems
            v.unlockFieldItems = this.unlockFieldItems
            v.addOptions = this.addOptions
            v.removeOptions= this.removeOptions
            v.addFields = this.addFieldItems
            v.removeFields = this.removeFields
            v.getLatestConfig = this.getLatestConfig
            v.lockOptionItems = this.lockOptionItems
            v.unlockOptionItems = this.unlockOptionItems

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
            this.config.lockFields && (this.lockFields = this.config.lockFields)

            if(this.fields.includes("")) {
                this.fields.splice(this.fields.indexOf(""),1)
            }
        } else {
            alert("Channel Encoder FATAL_RUN_TIME_ERROR: Config props is missing")
        }
        
        this.fields.map(e => {
            if(e != "") {
                this.value[e] = undefined
            }
        })

        this.sessionId = this.randomString(8)

        if(this.data) {
            this.ready = true
            this.value = this.data
            this.$emit('onMount', { 
                channel: {
                    changeOperationType: this.channel().changeOperationType,
                    lockFieldItems: this.channel().lockFieldItems,
                    unlockFieldItems: this.channel().unlockFieldItems,
                    addOptions:  this.channel().addOptions,
                    removeOptions: this.channel().removeOptions,
                    addFields: this.channel().addFields,
                    removeFields: this.channel().removeFields,
                    getLatestConfig: this.channel().getLatestConfig,
                    lockOptionItems: this.channel().lockOptionItems,
                    unlockOptionItems: this.channel().unlockOptionItems
                }
            })
        }

        setTimeout(() => {
            this.mount = true
        }, 1);
    }
}
</script>

<style scoped>
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
