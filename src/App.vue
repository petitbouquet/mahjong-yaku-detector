<script setup>
    import { ref, watch, onBeforeMount } from 'vue'
    import { flattenDeep } from 'lodash-es'

    let mjTehaiStrings = ref('')
    let isValid = ref(false)

    let mjActions = ref({
        isReached: false,
        isDoubleReached: false,
        isTsumo: false,
        isIppatu: false,
        isHaitei: false,
        isRinsyan: false,
        isChankan: false
    })

    let mjBlocks = ref([
        { kind: 'manzu', type: 'syuntsu', isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: false, values: [1, 2, 3] },
        { kind: 'souzu', type: 'syuntsu', isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: false, values: [1, 2, 3] },
        { kind: 'pinzu', type: 'syuntsu', isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: false, values: [1, 2, 3] },
        { kind: 'ton', type: 'koutsu', isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: true, values: [1, 1, 1] },
        { kind: 'haku', type: 'jyanto', isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: true, values: [1, 1] },
    ])

    function tehaiValidator () {
        let sevenToitsu = mjBlocks.value.filter(b => b.values.length == 2).length == 7
        let hasOneJantou = mjBlocks.value.filter(b => b.values.length == 2).length == 1
        let hasFourBlock = mjBlocks.value.filter(b => b.values.length == 3 || b.values.length == 4).length == 4

        isValid = sevenToitsu || (hasOneJantou && hasFourBlock)
    }

    watch(mjBlocks, () => tehaiValidator(), { deep: true })
    watch(mjActions, () => tehaiValidator(), { deep: true })
    onBeforeMount(() => tehaiValidator())
</script>

<template>
  <div>
    <p>hello</p>
    <input v-model="mjTehaiStrings">
    <p>mjTehaiStrings: {{mjTehaiStrings}} </p>
    <div>
        {{ JSON.stringify(mjActions) }}
    </div>
    <p>mjActions Button</p>
    <div>
        <button @click="mjActions.isReached = !mjActions.isReached">isReached</button>
        <button @click="mjActions.isDoubleReached = !mjActions.isDoubleReached">isDoubleReached</button>
        <button @click="mjActions.isTsumo = !mjActions.isTsumo">isTsumo</button>
        <button @click="mjActions.isIppatu = !mjActions.isIppatu">isIppatu</button>
        <button @click="mjActions.isHaitei = !mjActions.isHaitei">isHaitei</button>
        <button @click="mjActions.isRinsyan = !mjActions.isRinsyan">isRinsyan</button>
        <button @click="mjActions.isChankan = !mjActions.isChankan">isChankan</button>
    </div>
    <br>
    <div>
        {{ JSON.stringify(mjBlocks) }}
        <br>
        <span>{{ isValid }}</span>
        <br>
        <span>tehaiCount: {{ flattenDeep(mjBlocks.map(b => b.values)).length }}</span>
        <br>
        <span>blocks: {{ mjBlocks.filter(b => b.values.length === 3).length }}</span>
        <br>
        <span>jantou: {{ mjBlocks.filter(b => b.values.length === 2).length }}</span>
    </div>
  </div>
</template>

<style scoped>
</style>