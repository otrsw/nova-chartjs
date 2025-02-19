<template>
    <div>
        <div class="flex border-b border-40">
            <div v-show="field.showLabel" class="w-1/4 py-4">
                <h4 class="font-normal text-80">{{ field.name }}</h4>
            </div>
            <div class="w-3/4 py-4 flex-grow">
                <div class="flex border-b border-40">
                    <div class="w-1/4 py-4"><h4 class="font-normal text-80">Select another {{field.model}} to compare</h4></div>
                    <div class="w-3/4 py-4">
                        <multiselect
                            v-model = "selected"
                            :multiple = "true"
                            :searchable = "true"
                            group-values="groupItems"
                            group-label="groupLabel"
                            placeholder="Add items for comparison"
                            :group-select = "true"
                            :label="field.settings.titleProp"
                            track-by="id"
                            :options = "comparisonList"
                        />
                    </div>
                </div>
                <div class="flex border-b border-40">
                    <div class="w-full py-4">
                        <chartjs-range-chart
                            :dataset="comparisonDataset"
                            :additionalDatasets="field.additionalDatasets"
                            :settings="field.settings"
                            :height="field.settings.height"
                            :width="field.settings.width"
                        />
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import ChartjsRangeChart from "./ChartjsRangeChart";
import Multiselect from 'vue-multiselect';
import colors from "../mixins/colors";
import datasetHandler from "../mixins/datasetHandler";

export default {
    components: {
        Multiselect,
        ChartjsBarChart
    },

    mixins: [colors, datasetHandler],

    props: ['resource', 'resourceName', 'resourceId', 'field'],

    data() {
        return {
            selected: []
        }
    },

    methods: {
        isNotUser: function(element, index, array){
            return element[this.field.settings.identProp] != this.field.ident;
        },

        getDatapoint: function(values, title, color){
            if(!color){
                color = this.getRandomColor();
            }

            if(!values || (typeof values != 'object')){
                values = [];
            }

            return {
                label: title,
                borderColor: color,
                data: this.getAllowedParametersFromDataset(this.field.settings.parameters, values)
            }
        }
    },

    computed: {
        comparisonDataset: function(){
            let chartData = [];
            if(! this.field.notEditable || Object.keys(this.field.value).length){
                chartData.push(this.getDatapoint(this.field.value, this.field.title, this.field.settings.color));
            }

            return [
                ...chartData,
                ...this.selected.map(
                    data => this.getDatapoint(
                        data.nova_chartjs_metric_value.metric_values,
                        data[this.field.settings.titleProp]
                    )
                )
            ];
        },

        comparisonList: function(){
            return [
                {
                    groupLabel: 'Select/Deselect All',
                    groupItems: this.field.comparison.filter(this.isNotUser)
                },
            ];
        },

        valueDataset: function () {
            return {
                labels: Object.keys(this.field.value),
                datasets: Object.values(this.field.value)
            }
        },
    }
}
</script>
