<script>
export default {
    data() {
        return {
            currentState: 'default',
            selectedState: '',
            stateName: ' ',
            states: [" ", "Office", "WFH", "Remote", "PTO"],
            remoteNote: 'Country',
            count: 0
        };
    },
    methods: {
        setState(newState) {
            if (!this.selectedState) {
                this.currentState = newState;
            }
        },
        resetState() {
            if (!this.selectedState) {
                this.currentState = 'default';
            } else {
                this.currentState = 'active-' + this.selectedState.toLowerCase();
                this.setStateName(this.selectedState)
            }
        },
        updateStateName(newText, stateType) {
            if (newText == 'blank') {
                if (!this.selectedState) {
                    this.stateName = ' '
                    this.currentState = stateType + '-' + newText.toLowerCase();
                }
            } else {
                this.currentState = stateType + '-' + newText.toLowerCase();
                this.stateName = newText
            }
        },
        setStateName(stateName) {
            event.stopPropagation();
            this.updateStateName(stateName, 'active');
            this.selectedState = stateName
        },
        handleChange(event) {
            console.log("Input changed, value:", event.target.value);
        },
        alternativeClickThrough() {
            let localState
            this.count++
            localState = this.states[this.count]
            if (this.count == 5) {
                this.count = 0
            }
            if (this.count == 0) {
                this.currentState = 'hover-blank'
                this.stateName = this.states[0]
                this.selectedState = ''
            } else {
                this.setStateName(localState)
            }
        }
    },
    computed: {
        currentClass() {
            return this.currentState;
        },
    },
    props: {
        calDate: {
            type: Object,
            required: true,
            default: () => ({
                day: 'Mon',
                date: '1',
            })
        }
    }
};
</script>

<template>
    <div class="default" @mouseenter="setState('hover-blank')" @mouseleave="resetState" :class="currentClass"
        :style="{ pointerEvents: this.calDate.date === null ? 'none' : 'auto' }" @click="alternativeClickThrough()">
        <div class="status-options" @mouseleave="updateStateName('blank', 'hover')">
            <img src="../assets/icons/office.svg" alt="working from office"
                @mouseover="updateStateName(states[1], 'hover')" @click="setStateName(states[1])" class="office" />
            <img src="../assets/icons/wfh.svg" alt="working from home" @mouseover="updateStateName(states[2], 'hover')"
                @click="setStateName(states[2])" class="wfh" />
            <img src="../assets/icons/remote.svg" alt="working remotely"
                @mouseover="updateStateName(states[3], 'hover')" @click="setStateName(states[3])" class="remote" />
            <img src="../assets/icons/pto.svg" alt="out of office" @mouseover="updateStateName(states[4], 'hover')"
                @click="setStateName(states[4])" class="pto" />
        </div>
        <h2>{{ calDate.day }}</h2>
        <h3>{{ stateName }}</h3>
        <input v-if="this.selectedState == 'Remote' && this.stateName == 'Remote'"
            @change="handleChange" @click.stop v-model="remoteNote" />
    </div>
</template>

<style scoped lang="scss">
.default {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    row-gap: 8px;
    padding-top: 16px;
    height: 112px;
    width: 138px;
    cursor: pointer;
    user-select: none;

    .status-options {
        display: flex;
        opacity: 0;
        transition: all 0.3s ease;
        width: 112px;
        justify-content: center;

        img {
            height: 20px;
            padding: 0px 4px;
            cursor: pointer;
            opacity: 0.5;
            transition: all 0.2s ease;

            &:hover {
                opacity: 1;
            }
        }
    }

    h2 {
        font-size: 20px;
        font-weight: 700;
        color: var(--default-base-color);
    }

    h3 {
        opacity: 0;
        font-size: 12px;
        transition: all 0.3s ease;
    }

    input {
        opacity: 0.5;
        font-size: 10px;
        font-family: 'Martian Mono';
        font-style: normal;
        background: transparent;
        border: none;
        outline: none;
        border-bottom: 1px solid transparent;
        text-align: center;
        transition: border-color 0.3s;
        width: 75%;
        height: 15px;

        &:hover {
            border-bottom: 1px solid var(--remote-base-color);
        }

        &:focus {
            border-bottom: 1px solid var(--remote-base-color);
        }
    }
}

.hover-blank {

    .status-options {
        opacity: 1;
    }

    h3 {
        opacity: 0.5;
    }
}

.hover-office {


    background-color: var(--office-offbase-color);

    .status-options {
        opacity: 1;
    }

    h3 {
        opacity: 0.5;
    }

}

.hover-pto {


    background-color: var(--pto-offbase-color);

    .status-options {
        opacity: 1;
    }

    h3 {
        opacity: 0.5;
    }

}

.hover-wfh {


    background-color: var(--wfh-offbase-color);

    .status-options {
        opacity: 1;
    }

    h3 {
        opacity: 0.5;
    }

}

.hover-remote {

    background-color: var(--remote-offbase-color);

    .status-options {
        opacity: 1;
    }

    h3 {
        opacity: 0.5;
    }
}

.active-office {


    background-color: var(--office-offbase-color);

    .status-options,
    h3 {
        opacity: 1;
        color: var(--office-base-color);

        .office {
            display: block;
            opacity: 1;
        }

        .pto,
        .remote,
        .wfh {
            display: none;
        }
    }
}

.active-remote {


    background-color: var(--remote-offbase-color);

    .status-options,
    h3 {
        opacity: 1;
        color: var(--remote-base-color);

        .remote {
            display: block;
            opacity: 1;
        }

        .pto,
        .office,
        .wfh {
            display: none;
        }
    }

}

.active-pto {

    background-color: var(--pto-offbase-color);

    .status-options,
    h3 {
        opacity: 1;
        color: var(--pto-base-color);

        .pto {
            display: block;
            opacity: 1;
        }

        .remote,
        .office,
        .wfh {
            display: none;
        }
    }

}

.active-wfh {
    background-color: var(--wfh-offbase-color);

    .status-options,
    h3 {
        opacity: 1;
        color: var(--wfh-base-color);

        .wfh {
            display: block;
            opacity: 1;
        }

        .remote,
        .office,
        .pto {
            display: none;
        }
    }

}
</style>
