<script setup>
    import { ref, watch, onBeforeMount } from 'vue'
    import { flattenDeep } from 'lodash-es'

    const KIND = {
        MANZU: 'manzu',
        SOUZU: 'souzu',
        PINZU: 'minzu',
        YAKUHAI: 'yakuhai'
    }

    const TYPE = {
        SYUNTSU: 'syuntsu',
        KOUTSU: 'koutsu',
        KANTSU: 'kantsu',
        TOITSU: 'toitsu',
        JYANTOU: 'jyantou'
    }

    const FU_MAPPER = {
        FUTEI: 20,
        MENZEN_RON: 10,
        TSUMO: 2,
        JYANTOU: 0,
        JYANTOU_YAKUHAI: 2,
        YAOTYU_RATE: 2,
        SYUNTSU: 0,
        // RON MINKO, TSUMO ANKO
        MINKO: 2,
        ANKO: 4,
        MINKAN: 8,
        ANKAN: 16,
        FU_PINFU: 20,
        FU_CHITOITSU: 25,
        // APPLY IF FIND SINGLE MACHI PATTERN
        SINGLE_MACHI: 2
    }

    let mjTehaiStrings = ref('')
    let isValid = ref(false)
    let fu = ref(0)

    let mjActions = ref({
        isReached: false,
        isDoubleReached: false,
        isTsumo: false,
        isIppatu: false,
        isHaitei: false,
        isRinsyan: false,
        isChankan: false,
        isMenzen: false
    })

    let mjBlocks = ref([
        { kind: KIND.MANZU, type: TYPE.SYUNTSU, isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: false, values: [1, 2, 3] },
        { kind: KIND.SOUZU, type: TYPE.SYUNTSU, isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: false, values: [1, 2, 3] },
        { kind: KIND.SOUZU, type: TYPE.SYUNTSU, isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: false, values: [1, 2, 3] },
        { kind: KIND.YAKUHAI, type: TYPE.KOUTSU, isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: true, values: [1, 1, 1] },
        { kind:KIND.YAKUHAI, type: TYPE.JYANTOU, isCalled: false, callType: undefined, hasYaoTyu: true, isOnlyYaoTyu: true, values: [1, 1] },
    ])

    function tehaiValidator () {
        let sevenToitsu = mjBlocks.value.filter(b => b.values.length == 2).length == 7
        let hasOneJantou = mjBlocks.value.filter(b => b.values.length == 2).length == 1
        let hasFourBlock = mjBlocks.value.filter(b => b.values.length == 3 || b.values.length == 4).length == 4

        isValid = sevenToitsu || (hasOneJantou && hasFourBlock)
    }

    function countFu () {
        let fu_temp = FU_MAPPER.FUTEI

        const actions = mjActions.value
        if (actions.isTsumo) fu_temp += FU_MAPPER.TSUMO
        if (!actions.isTsumo && actions.isMenzen) fu_temp += FU_MAPPER.MENZEN_RON
        
        // TODO PINFU AND CHITOISTU DETECT
        mjBlocks.value.forEach(b => {
            const fu_rate = b.hasYaoTyu ? FU_MAPPER.YAOTYU_RATE : 1
            if (b.type === TYPE.SYUNTSU) fu += FU_MAPPER.SYUNTSU * fu_rate
            if (b.type === TYPE.KOUTSU) {
                fu_temp += b.isCalled ? FU_MAPPER.MINKO : FU_MAPPER.ANKO * fu_rate
            }
            if (b.type === TYPE.KANTSU) {
                fu_temp += b.isCalled ? FU_MAPPER.MINKAN : FU_MAPPER.MINKO * fu_rate
            }
            if (b.type === TYPE.JYANTOU) {
                fu_temp += b.kind === KIND.YAKUHAI ? FU_MAPPER.JYANTOU_YAKUHAI : FU_MAPPER.JYANTOU * fu_rate
            }
        })

        console.log(fu_temp)
        fu = Math.ceil(fu_temp / 10) * 10
    }

    function calcProcess () {
        tehaiValidator()
        countFu()     
    }

    watch(mjBlocks, () => calcProcess, { deep: true })
    watch(mjActions, () => calcProcess(), { deep: true })
    onBeforeMount(() => calcProcess())
</script>

<template>
  <div>
    <p>hello</p>
    <input v-model="mjTehaiStrings">
    <p>mjTehaiStrings: {{mjTehaiStrings}} </p>
    <p>fu: {{ fu }}</p>
    <div>
        {{ JSON.stringify(mjActions) }}
    </div>
    <p>mjActions Button</p>
    <div>
        <button @click="mjActions.isMenzen = !mjActions.isMenzen">isMenzen</button>
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