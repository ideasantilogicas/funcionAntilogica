<!--**********************************************************************************************
*                                                                                                *
*               TEMPLATE/HTML AREA                                                               *
*                                                                                                *
***********************************************************************************************-->
<template>

    <div>
        <!--*********************
        *    FUNCTION INPUT     *
        **********************-->
        <div class="function">antilogicalWaves
            ( input: <input type="text" id="functionInput" size="3" class="functionInput" v-model="inputValue" @keyup="runWaves()"> )
        </div>
        <!--***********************
        *    PARENTESIS/WAVES     *
        ************************-->
        <div class="parentesis">
            <span v-for="element in results" :key="element.result" :style="element.parentesisStyle">) </span>
        </div>
        <!--**********************************************************
        *    MAIN CONTAINER (OUTPUTS, DISTANCES, FUNCTION IMAGE)     *
        ***********************************************************-->
        <div class="mainContainer">
            <!--*****************************************************************
            *    OUTPUT LIST                                                    *
            *       TO GET EVERY NUMBER, THE FUNCTION IS EXECUTED input TIMES   *
            *       EVERY OUTPUT IS PUSHED INTO THE results ARRAY               *
            ******************************************************************-->
            <div class="functionOutput" v-if="results.length > 0">
                <div>Outputs (from 1 to {{results.length}}):</div>
                <span v-for="output in results" :key="output.result">{{ output.result }}, </span>
            </div>
            <br>
            <!--***************************************************************************
            *    DISTANCES LIST                                                           *
            *       - EVERY DISTANCE IS CALCULATED SUBTRACTING                            *
            *           THE CURRENT RESULT TO THE PREVIOUS RESULT (EXCEPT THE FIRST ONE)  *
            *       - EVERY DISTANCE IS PUSHED INTO THE distancesBetweenResults ARRAY     *
            ****************************************************************************-->
            <div class="distancesBetweenResults" v-if="distancesBetweenResults.length > 0">
                <div>Distances between results:</div>
                <span v-for="element in distancesBetweenResults" :key="element.previousIndex">{{ element.distance }}, </span>
            </div>
            <!--*************************************
            *     IMAGE THAT SHOWS THE FUNCTION     *  
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
        /*************************************************************
         *   EXECUTES THE antilogicalWaves(input) inputValue TIMES   *
         ************************************************************/
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
        /********************************************************************************************
         *  antilogicalWaves(input):                                                                *
         *      -RECURSIVE FUNCTION THAT CALCULATES ITS RESULT                                      *
         *          DOING CONSECUTIVE CALLS TO ITSELF WITH THE INPUT LESS 1                         *
         *      - WHEN THE input FINALLY GETS TO BE 0, BEGINS THE CONSECUTIVE RETURNS,              *
         *          ADDING EACH RETURN TO THE FINAL RESULT                                          *
         *                                                                                          *
         *  WANNA TRY TO UNDERESTAND THIS? LET'S GO!!                                               *
         *                                                                                          *
         *  EXAMPLE FOR antilogicalWaves(3):                                                        *
         *  ===============================                                                         *
         *                                                                                          *
         *  RESULT: 6                                                                               *
         *                                                                                          *
         *  CALL STACK:                                                                             *
         *      TOP OF THE STACK>   antilogicalWaves(3)                                             *
         *                          antilogicalWaves(2)                                             *
         *                          antilogicalWaves(1)                                             *
         *                          antilogicalWaves(0)    <BOTTOM OF THE STACK                     *
         *                                                                                          *
         *  RESOLUTION:                                                                             *
         *  antilogicalWaves(3) = 3 + antilogicalWaves(2)                                           *
         *                            >> return 2 + antilogicalWaves(1)                             *
         *                                          >> return 1 + antilogicalWaves(0)               *
         *                                                        >>    return 0                    *
         *                                                                                          *
         *  EXPLANATION:                                                                            *
         *          antilogicalWaves(3) WILL return (3 + antilogicalWaves(2))                       *
         *          BUT, TO GET THE FINAL RESULT, WILL NEED TO EXECUTE antilogicalWaves(2) FIRST    *
         *          SO, antilogicalWaves(2) WILL return (2 + antilogicalWaves(1))                   *
         *          BUT, TO GET THE RESULT, WILL NEED TO EXECUTE antilogicalWaves(1) FIRST          *
         *          SO, antilogicalWaves(1) WILL return (1 + antilogicalWaves(0))                   *
         *          BUT, TO GET THE RESULT, WILL NEED TO EXECUTE antilogicalWaves(0) FIRST          *
         *          FINALLY, antilogicalWaves(0) WILL ALWAYS RETURN 0.                              *
         *          AT THIS POINT, EVERY antilogicalWaves() CALL IS WAITING TO BE RESOLVED.         *
         *          ONCE IT GETS TO RETURN 0, BEGINS THE BACKWARD RESOLUTION, IN OTHER WORDS,       *
         *          THE STACK BEGINS TO RESOLVE FROM BOTTOM TO TOP:                                 *
         *                                                                                          *
         *              SO, antilogicalWaves(0) RETURNS 0                                           *
         *              THEN: THE PREVIOUS CALL antilogicalWaves(1) RETURNS 1 BECAUSE:              *
         *                          1 + antilogicalWaves(0) RETURNS 1 (1 + 0)                       *
         *              THEN: THE PREVIOUS CALL antilogicalWaves(2) RETURNS 3 BECAUSE:              *
         *                          (2 + antilogicalWaves(1)) RETURNS 3 (2 + 1)                     *
         *              FINALLY: THE PREVIOUS CALL antilogicalWaves(3) RETURNS 6 BECAUSE:           *
         *                          (3 + antilogicalWaves(2)) RETURNS 6 (3 + 3)                     *
         *                                                                                          *
         *  STACK RESOLUTION SUMMARY (IN RECURSIVE FUNCTIONS IS ALWAYS FROM BOTTOM TO TOP):         *
         *                                                                                          *
         *          TOP>     3 + 3 = 6                                                              *
         *                   2 + 1 = 3                                                              *
         *                   1 + 0 = 1                                                              *
         *                   0          <BOTTOM                                                     *
         *                                                                                          *
         * TRY TO EXPLAIN antilogicalWaves(5) TO YOURSELF TO MASTER RECURSIVE FUNCTIONS!            *
         * 
         ********************************************************************************************/
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