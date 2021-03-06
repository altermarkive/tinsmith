<template>
  <v-main>
    <v-card class="d-flex flex-column ma-0 pa-0 fill-height" flat>
      <v-card class="mx-2 mt-3 mb-1 px-4 py-0" align="center">
        <v-row>
          <v-col v-for="key in parametersKeys" v-bind:key="key">
            <v-text-field
              v-model="parameters[key]"
              :min="0"
              dense
              :label="parametersLabels[key] + ' ='"
              v-bind:key="key"
            >
            </v-text-field>
          </v-col>
          <v-col>
            <v-dialog v-model="help" persistent>
              <template v-slot:activator="{ on, attrs }">
                <v-btn icon v-bind="attrs" v-on="on">
                  <v-icon>mdi-help-circle-outline</v-icon>
                </v-btn>
              </template>
              <v-card>
                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-btn icon @click="help = false">
                    <v-icon>mdi-close</v-icon>
                  </v-btn>
                </v-card-actions>
                <v-card-text align="center">
                  <v-img
                    max-height="70vh"
                    max-width="80vh"
                    contain
                    :src="require(`@/assets/${$props.blueprintName}.png`)"
                  />
                </v-card-text>
              </v-card>
            </v-dialog>
          </v-col>
        </v-row>
      </v-card>
      <v-card class="mx-2 mt-1 mb-3 px-4 py-4 flex-grow-1 d-flex align-stretch">
        <div>
          <span v-for="entry in featureComputed()" v-bind:key="entry.key">
            {{ entry.label }}&nbsp;=&nbsp;{{ entry.value }}<br />
          </span>
        </div>
      </v-card>
    </v-card>
  </v-main>
</template>

<script lang="ts">
import Vue from "vue";

interface ParametersEntries {
  [key: string]: string;
}

interface Parameters {
  [key: string]: number;
}

interface Results {
  [key: string]: number | number[];
}

interface ResultEntry {
  key: string;
  label: string;
  value: string;
}

interface ResultsLabels {
  [key: string]: string;
}

interface ResultsDigits {
  [key: string]: number;
}

function initiateParameters(keys: string[]): ParametersEntries {
  const initiated: ParametersEntries = {};
  keys.forEach((key: string) => {
    initiated[key] = "";
  });
  return initiated;
}

function prepareParameters(unprepared: ParametersEntries): Parameters {
  const prepared: Parameters = {};
  Object.keys(unprepared).forEach((key: string) => {
    prepared[key] = parseFloat(unprepared[key]) || NaN;
  });
  return prepared;
}

function prepareResult(
  composite: Results,
  key: string,
  digits: number
): string {
  if (composite != null) {
    if (Array.isArray(composite[key])) {
      const vector: number[] = composite[key] as number[];
      return vector.map((item: number) => item.toFixed(digits)).join(", ");
    }
    if (typeof composite[key] === "number") {
      const scalar: number = composite[key] as number;
      return scalar.toFixed(digits);
    }
  }
  return "";
}

function prepareResults(
  composite: Results,
  keys: string[],
  labels: Record<string, string>,
  digits: Record<string, number>
) {
  const prepared: ResultEntry[] = keys.map((key: string) => {
    const entry: ResultEntry = {
      key: key,
      label: labels[key],
      value: prepareResult(composite, key, digits[key]),
    };
    return entry;
  }) as ResultEntry[];
  return prepared;
}

export default Vue.extend({
  name: "Feature",

  props: {
    parametersKeys: {
      type: Array,
      required: true,
    },
    parametersLabels: {
      type: Object,
      required: true,
    },
    resultsKeys: {
      type: Array,
      required: true,
    },
    resultsLabels: {
      type: Object,
      required: true,
    },
    resultsDigits: {
      type: Object,
      required: true,
    },
    coreFunction: {
      type: Function,
      required: true,
    },
    blueprintName: {
      type: String,
      required: true,
    },
  },

  data: () => ({
    parameters: {} as ParametersEntries,
    help: false,
  }),

  mounted() {
    this.parameters = initiateParameters(this.parametersKeys as string[]);
  },

  methods: {
    featureComputed() {
      return prepareResults(
        JSON.parse(
          this.coreFunction(JSON.stringify(prepareParameters(this.parameters)))
        ),
        this.resultsKeys as string[],
        this.resultsLabels as ResultsLabels,
        this.resultsDigits as ResultsDigits
      );
    },
  },
});
</script>
