<template>
    <span>
        <chartjs-bar-chart
            :height="60"
            :width="300"
            :data="valueDataset"
        />
    </span>
</template>

<script>
import ChartjsBarChart from "./ChartjsBarChart";
import colors from "../mixins/colors";
import datasetHandler from "../mixins/datasetHandler";

export default {
    components: {ChartjsBarChart},

    mixins: [colors, datasetHandler],

    props: ['resourceName', 'field'],

    computed: {
        valueDataset: function () {
            let color = this.field.settings.indexColor || this.field.settings.color || this.getRandomColor();
            let colors = Array(this.field.settings.parameters.length).fill(color);
            return {
                labels: this.field.settings.parameters,
                datasets: [
                    {
                        backgroundColor: colors,
                        'data': this.getAllowedParametersFromDataset(this.field.settings.parameters, this.field.value)
                    }
                 ]
            }
        },
    }
}
</script>
