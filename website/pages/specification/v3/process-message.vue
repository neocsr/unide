<template>
  <div class="process-message content">
    <h1>
      <a id="Process-Payload" title="Process Message payload"></a>
      Process Payload
    </h1>
    <p>The process message is the format to exchange data out of discrete processes. It also allows to transport process information, part information and measurement data for each phase of the process.</p>
    <img src="images/specification/v3/processPayload.svg" alt="Class diagram of the Process message payload" title="Class diagram of the Process Message payload" class="is-center">

    <schemaDetail type="v3/process" :examples="$static.examples">
      <card :collapsed="true">
        <template slot="header">
          Minimal message example 
        </template>
        <prism language="json">{{ $static.message | stringify }}</prism>
      </card>

      <card :collapsed="true">
        <template slot="header">
          Process message example 
        </template>
        <prism language="json">{{ $static.complexMessage | stringify }}</prism>
      </card>
    </schemaDetail>
  </div>
</template>

<script>
import prism from "vue-prism-component";
import card from "~/components/collapsibleCard.vue";
import get from "lodash/get";
import schemaDetail from "~/components/schemaDetail.vue";

export default {
  head() {
    return {
      title: "Specification for process messages"
    };
  },
  created() {
    const now = new Date(),
      deviceId = "a4927dad-58d4-4580-b460-79cefd56775b";
    this.$static = {
      message: {
        "content-spec": "urn:spec://eclipse.org/unide/process-message#v2",
        device: {
          id: deviceId
        },
        process: {
          ts: now.toISOString()
        },
        measurements: [
          {
            ts: new Date(now.valueOf() + 100).toISOString(),
            series: {
              time: [0, 23, 24],
              force: [26, 23, 24],
              pressure: [52.4, 46.32, 44.2432]
            }
          }
        ]
      },
      complexMessage: {
        "content-spec": "urn:spec://eclipse.org/unide/process-message#v2",
        device: {
          id: deviceId,
          mode: "auto",
          state: "OK",
          swVersion: "2.0.3.13",
          swBuildId: "41535"
        },
        part: {
          code: "HUH289",
          id: "420003844",
          result: "NOK",
          toolId: "32324-432143",
          type: "SINGLE",
          typeId: "F00VH07328"
        },
        process: {
          externalId: "b4927dad-58d4-4580-b460-79cefd56775b",
          program: {
            id: "1",
            lastChangedDate: "2002-05-30T09:30:10.123+02:00",
            name: "Programm 1"
          },
          result: "NOK",
          shutoffPhase: "phase 2",
          ts: now.toISOString(),
          escalation: "shift leader",
          maxDuration: "30min"
        },
        measurements: [
          {
            code: "0000 EE01",
            name: "heating up",
            phase: "phase 1",
            result: "OK",
            ts: new Date(now.valueOf() + 100).toISOString(),
            context: {
              pressure: {
                limits: {
                  upperError: 4444,
                  lowerError: 44,
                  upperWarn: 2222,
                  lowerWarn: 46,
                  target: 35
                }
              },
              force: {
                limits: {
                  upperError: [29, 27, 26],
                  lowerError: [23, 21, 20],
                  upperWarn: [28.5, 26.5, 25.5],
                  lowerWarn: [23.5, 21.5, 20.5],
                  target: [26, 24, 23]
                }
              }
            },
            specialValues: [
              {
                // eslint-disable-next-line camelcase
                time: 12,
                name: "turning point",
                value: {
                  pressure: 24,
                  force: 50
                }
              },
              {
                time: 50,
                name: "shutoffForce",
                value: {
                  force: 24,
                  upperError: 26,
                  lowerError: 22,
                  upperWarn: 25,
                  lowerWarn: 23,
                  target: 24
                }
              },
              {
                time: 50,
                name: "shutoffPressure",
                value: {
                  pressure: 50,
                  upperError: 52,
                  lowerError: 48
                }
              }
            ],
            series: {
              time: [30, 36, 42],
              force: [26, 23, 24],
              pressure: [52.4, 46.32, 44.2432],
              temperature: [45.4243, 46.42342, 44.2432]
            }
          },
          {
            ts: new Date(now.valueOf() + 430).toISOString(),
            phase: "phase 2",
            name: "processing",
            result: "OK",
            series: {
              // eslint-disable-next-line camelcase
              time: [0, 23, 24],
              temperature: [49.2, 48.8, 50]
            }
          }
        ]
      }
    };
    this.$static.examples = Object.entries({
      ...[
        "content-spec",
        "device",
        "device.id",
        "device.mode",
        "measurements",
        "part",
        "part.code",
        "part.id",
        "part.result",
        "part.type",
        "part.typeId",
        "process",
        "process.externalId",
        "process.program",
        "process.program.id",
        "process.program.lastChangedDate",
        "process.program.name",
        "process.result",
        "process.shutoffPhase",
        "process.ts"
      ].reduce(
        (l, v) => {
          l[this.schemafy(v)] = v;
          return l;
        },
        {
          'properties.measurements.allOf[0].items.properties.context.patternProperties["^[^$]+"].properties.limits.oneOf[0]':
            "measurements[0].context.pressure.limits",
          'properties.measurements.allOf[0].items.properties.context.patternProperties["^[^$]+"].properties.limits.oneOf[1]':
            "measurements[0].context.force.limits",
          'properties.measurements.allOf[0].items.properties.series.patternProperties["^[^$]+"]':
            "measurements[0].series.force"
        }
      ),
      ...["code", "result", "series", "series.time", "ts"].reduce((l, key) => {
        l[
          `properties.measurements.allOf[0].items.${this.schemafy(key)}`
        ] = `measurements[0].${key}`;
        return l;
      }, {}),
      ...[
        "name",
        "phase",
        "specialValues",
        "specialValues[1].time",
        "specialValues[1].name",
        "specialValues[1].value"
      ].reduce((l, key) => {
        l[
          `properties.measurements.allOf[1].items.${this.schemafy(key)}`
        ] = `measurements[0].${key}`;
        return l;
      }, {}),
      ...[
        "lowerError",
        "lowerWarn",
        "target",
        "upperError",
        "upperWarn"
      ].reduce((l, key) => {
        l[
          `properties.measurements.allOf[0].items.properties.context.patternProperties["^[^$]+"].properties.limits.oneOf[0].properties.${key}`
        ] = `measurements[0].context.pressure.limits.${key}`;
        l[
          `properties.measurements.allOf[0].items.properties.context.patternProperties["^[^$]+"].properties.limits.oneOf[1].properties.${key}`
        ] = `measurements[0].context.force.limits.${key}`;
        return l;
      }, {})
    }).reduce((l, [key, path]) => {
      const example =
        get(this.$static.message, path) ||
        get(this.$static.complexMessage, path);
      if (example) {
        l[key] = [example];
      } else {
        console.error(`no example provided in process-message for:
"${key}": "${path}"`);
      }
      return l;
    }, {});
  },
  filters: {
    stringify(v) {
      return JSON.stringify(v, " ", 2);
    }
  },
  methods: {
    schemafy(v) {
      return v
        .replace(/(^|\.)/g, "$1properties.")
        .replace(/\[[^]]*]/g, ".items");
    }
  },
  components: {
    card,
    prism,
    schemaDetail
  }
};
</script>
