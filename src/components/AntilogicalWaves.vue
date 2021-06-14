<!--**********************************************************************************************
*                                                                                                *
*               TEMPLATE/HTML AREA                                                               *
*                                                                                                *
***********************************************************************************************-->
<template>

    <div>
        <!--******************************
        *    ENTRADA/INPUT DE LA FUNCIÓN *
        *******************************-->
        <div class="function">antilogicalWaves
            ( input: <input type="text" id="functionInput" size="3" class="functionInput" v-model="inputValue" @keyup="runWaves()"> )
        </div>
        <!--***********************
        *    PARENTESIS/ONDAS     *
        ************************-->
        <div class="parentesis">
            <span v-for="element in results" :key="element.result" :style="element.parentesisStyle">) </span>
        </div>
        <!--*******************************************************************************
        *    CONTENEDOR FLEXBOX PRINCIPAL (SALIDAS, DISTANCIAS, IMAGEN DE LA FUNCIÓN)     *
        ********************************************************************************-->
        <div class="mainContainer">
            <!--*****************************************************************************
            *    LISTA DE SALIDAS                                                           *
            *       PARA OBTENER CADA NUMERO DE SALIDA, LA FUNCIÓN ES EJECUTADA input VECES *
            *       CADA SALIDA (output) ES INSERTADA EN EL ARRAY results                   *
            ******************************************************************************-->
            <div class="functionOutput" v-if="results.length > 0">
                <div>Outputs (from 1 to {{results.length}}):</div>
                <span v-for="output in results" :key="output.result">{{ output.result }}, </span>
            </div>
            <br>
            <!--******************************************************************************
            *    LISTA DE DISTANCIAS                                                         *
            *       - CADA SALIDA ES CALCULADA RESTANDO EL RESULTADO ACTUAL                  *
            *               MENOS EL RESULTADO ANTERIOR (EXCEPTO EL PRIMERO, OBVIAMENTE)     *
            *       - CADA DISTANCIA ES INSERTADA EN EL ARRAY distancesBetweenResults        *
            *******************************************************************************-->
            <div class="distancesBetweenResults" v-if="distancesBetweenResults.length > 0">
                <div>Distances between results:</div>
                <span v-for="element in distancesBetweenResults" :key="element.previousIndex">{{ element.distance }}, </span>
            </div>
            <!--*************************************
            *     IMAGEN QUE MUESTRA LA FUNCIÓN     *  
            **************************************-->
            <div class="imgContainer">
                <img src="../assets/antilogicalWavesFunction.png" class="img">
            </div>
        </div>
    </div>
</template>
<!--*******************************************************************************************
*
*                   JAVASCRIPT AREA
*
*******************************************************************************************-->
<script>
export default {
    name: 'AntilogicalWaves',
    data: function() {
        return {
            inputValue              : '',
            results                 : [],
            distancesBetweenResults : []
        }
    },
    created: function() {
        window.onload = () => {
            document.getElementById('functionInput').focus()
        }
    },
    methods: {
        /*********************************************************************
         *   EJECUTA LA FUNCIÓN antilogicalWaves(input) LA CANTIDAD VECES    *
         *          LA CANTIDAD DE VECES INGRESADA POR TECLADO (inputValue)  *
         *********************************************************************/
        runWaves: function() {
            if(isNaN(parseInt(this.inputValue)) || parseInt(this.inputValue) < 1)
                return
            this.inputValue              = parseInt(this.inputValue)
            this.results                 = []
            this.distancesBetweenResults = []
            let previousIndex            = -1
            for(let i = 1; i <= this.inputValue; i++) {
                let result = this.antilogicalWaves(i)
                let parentesisStyle  = {
                    color         : 'aqua',
                    'font-family' : 'Verdana, Geneva, Tahoma, sans-serif',
                    'font-size'   : result + 'px',
                    position      : 'relative',
                    left          : (result - i) + 'px',
                    top           : 0 + 'px',
                    transition    : 'left 1s, top 1s'
                }
                this.results.push({ result, parentesisStyle })
                let distance = (previousIndex == -1)? 0 : (result - this.results[previousIndex].result)
                this.distancesBetweenResults.push({distance, index: i})
                previousIndex++
                setInterval(() => {
                    parentesisStyle.left = (Math.floor(Math.random() * (i/2)) + 1) + 'px'
                    parentesisStyle.top  = (Math.floor(Math.random() *     i) + 1) + 'px'
                }, 500)
            }
        },
        /************************************************************************************************
         *  antilogicalWaves(input):                                                                    *
         *      -FUNCIÓN RECURSIVA QUE CALCULA SU RESULTADO REALIZANDO LLAMADAS A SÍ MISMA              *
         *          CON EL NÚMERO RECIBIDO input RESTÁNDOLE 1 CADA VEZ                                  *
         *      - CUANDO input FINALMENTE LLEGA A 0, COMIENZAN LOS return CONSECUTIVOS,                 *
         *          SUMANDO CADA RETORNO AL RESULTADO FINAL                                             *
         *                                                                                              *
         *  QUIERES INTENTAR ENTENDER ESTO EN DETALLE? LET'S GO!!                                       *
         *                                                                                              *
         *  EJEMPLO PARA antilogicalWaves(3):                                                           *
         *  ================================                                                            *
         *                                                                                              *
         *  RESULTADO ESPERADO: 6                                                                       *
         *                                                                                              *
         *  PILA DE LLAMADAS SUCESIVAS A LA FUNCIÓN :                                                   *
         *                                                                                              *   
         *     PRIMERA LLAMADA>     antilogicalWaves(3)                                                 *   
         *                          |                                                                   *
         *     SEGUNDA LLAMADA>     |-> antilogicalWaves(2)                                             *
         *                              |                                                               *
         *     TERCERA LLAMADA>         |-> antilogicalWaves(1)                                         *
         *                                  |                                                           *
         *      ÚLTIMA LLAMADA>             |-> antilogicalWaves(0)                                     *
         *                                                                                              *
         *  RESOLUCIÓN:                                                                                 *
         *  antilogicalWaves(3) = 3 + antilogicalWaves(2)                                               *
         *                            |-> return 2 + antilogicalWaves(1)                                *
         *                                           |-> return 1 + antilogicalWaves(0)                 *
         *                                                          |-> return 0                        *
         *                                                                                              *
         *  EXPLICACIÓN:                                                                                *
         *          antilogicalWaves(3) DEBE RETORNAR: (3 + antilogicalWaves(2))                        *
         *          PERO, PARA OBTENER EL RESULTADO FINAL, PRIMERO DEBE EJECUTAR antilogicalWaves(2)    *
         *          ENTONCES: antilogicalWaves(2) DEBE RETORNAR: (2 + antilogicalWaves(1))              *
         *          PERO, PARA OBTENER EL RESULTADO, PRIMERO DEBE EJECUTAR antilogicalWaves(1)          *
         *          ENTONCES: antilogicalWaves(1) DEBE RETORNAR (1 + antilogicalWaves(0))               *
         *          PERO, PARA OBTENER EL RESULTADO, PRIMERO DEBE EJECUTAR antilogicalWaves(0)          *
         *          FINALMENTE, LA EJECUCIÓN DE antilogicalWaves(0) RETORNA 0 COMO SIEMPRE.             *
         *                                                                                              *
         *          EN ESTE PUNTO, CADA LLAMADA A LA FUNCIÓN antilogicalWaves() ESTÁ ESPERANDO          *
         *          AL RESTO PARA RESOLVERSE.                                                           *
         *          UNA VEZ QUE SE LLEGA A LA LLAMADA QUE RETORNA 0, COMIENZA LA RESOLUCIÓN DE CADA     *
         *          LLAMADA (EN ORDEN INVERSO), DICHO DE OTRA MANERA,                                   *
         *          LA PILA DE LLAMADAS EMPIEZA A RESOLVERSE DESDE LA ÚLTIMA LLAMADA                    *
         *          HASTA LA PRIMERA:                                                                   *
         *                                                                                              *
         *              ENTONCES: antilogicalWaves(0) RETORNA 0                                         *
         *              LUEGO: LA LLAMADA ANTERIOR antilogicalWaves(1) RETORNA 1 PORQUE:                *
         *                          1 + antilogicalWaves(0) ES LO MISMO QUE (1 + 0)                     *
         *              LUEGO: LA LLAMADA ANTERIOR antilogicalWaves(2) RETORNA 3 PORQUE:                *
         *                          (2 + antilogicalWaves(1)) ES LO MISMO QUE (2 + 1)                   *
         *              FINALMENTE: LA LLAMADA ANTERIOR antilogicalWaves(3) RETORNA 6 PORQUE:           *
         *                          (3 + antilogicalWaves(2)) ES LO MISMO QUE (3 + 3)                   *
         *                                                                                              *
         *  RESUMEN DE LLAMADAS A LA FUNCIÓN antilogicalWaves(3)                                        *
         *      (LAS FUNCIONES RECURSIVAS SE RESUELVEN DESDE LA ÚLTIMA LLAMADA HACIA LA PRIMERA):       *
         *                                                                                              *
         *         PRIMERA>  3 + 3 = 6                                                                  *
         *         SEGUNDA>  2 + 1 = 3                                                                  *
         *         TERCERA>  1 + 0 = 1                                                                  *
         *          ÚLTIMA>          0                                                                  *
         *                                                                                              *
         * TRATA DE EXPLICAR antilogicalWaves(5) A TI MISMO Y DOMINAR LAS FUNCIONES RECURSIVAS!         *
         *                                                                                              *
         ************************************************************************************************/
        antilogicalWaves: function(input) {
            if(input == 0)
                return 0
            return input + this.antilogicalWaves(input - 1)
        }
    }
}
</script>
<!--*******************************************************************************************
*
*                   STYLE/CSS AREA
*
*******************************************************************************************-->
<style scoped>
    .imgContainer {
        display: flex;
        justify-content: flex-end;
    }
    .mainContainer {
        display: flex;
        flex-direction: column;
        justify-content: flex-end;
    }
    .img {
        border: solid 5px lime;
        border-radius: 50px;
        position: absolute;
        bottom: 50px;
        padding: 8px;
    }
    .functionInput {
        font-size: 25px;
        font-family: 'Stalinist One', cursive;
        background-color: black;
        color: lime;
        border-radius: 10px;
    }
    .parentesis {
        border-bottom: solid 1px yellow;
        padding-bottom: 55px;
    }
    .function {
        font-size: 20px;
        font-family: 'Stalinist One', cursive;
    }

    .functionOutput, .distancesBetweenResults {
        font-size: 20px;
        font-family: 'Stalinist One', cursive;
        margin-top: 15px;
    }
</style>