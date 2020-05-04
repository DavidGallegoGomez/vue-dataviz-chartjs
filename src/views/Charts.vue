<template>
  <div>
    <h1>Montañas rusas: los datos</h1>
    <p>Visualización de estadísticas globales</p>
    <hr />
    <h2 v-if="loading">Esperando respuesta de la API...</h2>

    <template v-else>
      <section class="cols-2">
        <charto
          type="Gráfico radial"
          name="Comparativa global"
          id="chart1"
          :data="chart1"
        />
        <charto
          type="Gráfico de área polar"
          name="País"
          id="chart2"
          :data="chart2"
        />
      </section>
      <section class="cols-1">
        <charto
          type="Gráfico lineal"
          name="Construcciones por año"
          id="chart6"
          :data="chart6"
        />
      </section>
      <section class="cols-3">
        <charto
          type="Gráfico radial"
          name="Altura"
          id="chart3"
          :data="chart3"
        />
        <charto
          type="Gráfico de donut"
          name="Modelos"
          id="chart4"
          :data="chart4"
        />
        <charto
          type="Gráfico de barras"
          name="Fuerza G"
          id="chart5"
          :data="chart5"
        />
      </section>
    </template>
  </div>
</template>
<script>
import Charto from "@/components/Charto.vue";
import { styles } from "@/styles/chartStyles";

export default {
  components: {
    Charto
  },
  data: () => ({
    coaster: null,
    loading: null,
    chart1: null,
    chart2: null,
    chart3: null,
    chart4: null,
    chart5: null,
    chart6: null
  }),
  methods: {
    async getCoaster() {
      this.loading = true;
      await fetch("http://coasters-api.herokuapp.com/")
        .then(response => response.json())
        .then(data => (this.coaster = data));
      this.loading = false;
      this.chart1 = this.dataChart1;
      this.chart2 = this.dataChart2;
      this.chart3 = this.dataChart3;
      this.chart4 = this.dataChart4;
      this.chart5 = this.dataChart5;
      this.chart6 = this.dataChart6;
    }
  },
  computed: {
    dataChart1() {
      const selectedCoaster = this.coaster.filter(coaster => coaster.gForce);
      return {
        type: "radar",
        data: {
          labels: selectedCoaster.map(coaster => coaster.name),
          datasets: [
            {
              label: "Altura",
              data: selectedCoaster.map(coaster => coaster.height),
              borderColor: styles.color.solids[0],
              backgroundColor: styles.color.alphas[0]
            },
            {
              label: "Longitud",
              data: selectedCoaster.map(coaster => coaster.length),
              borderColor: styles.color.solids[1],
              backgroundColor: styles.color.alphas[1],
              hidden: true
            },
            {
              label: "Inversiones",
              data: selectedCoaster.map(coaster => coaster.inversions),
              borderColor: styles.color.solids[2],
              backgroundColor: styles.color.alphas[2]
            },
            {
              label: "Velocidad",
              data: selectedCoaster.map(coaster => coaster.speed),
              borderColor: styles.color.solids[3],
              backgroundColor: styles.color.alphas[3]
            },
            {
              label: "Fuerza G",
              data: selectedCoaster.map(coaster => coaster.gForce),
              borderColor: styles.color.solids[4],
              backgroundColor: styles.color.alphas[4]
            }
          ]
        },
        options: {
          legend: {
            position: "left"
          }
        }
      };
    },
    dataChart2() {
      return {
        type: "polarArea",
        data: {
          labels: ["USA", "UK", "Spain", "Japan", "China"],
          datasets: [
            {
              data: [
                this.coaster.filter(
                  coaster => coaster.country === "United States"
                ).length,
                this.coaster.filter(
                  coaster => coaster.country === "United Kingdom"
                ).length,
                this.coaster.filter(coaster => coaster.country === "Spain")
                  .length,
                this.coaster.filter(coaster => coaster.country === "Japan")
                  .length,
                this.coaster.filter(coaster => coaster.country === "China")
                  .length
              ],
              borderColor: styles.color.solids,
              backgroundColor: styles.color.alphas
            }
          ]
        },
        options: {
          legend: {
            position: "right"
          }
        }
      };
    },
    dataChart3() {
      const selectedCoaster = this.coaster.filter(
        coaster => coaster.height > 80
      );
      return {
        type: "radar",
        data: {
          labels: selectedCoaster.map(coaster => coaster.name),
          datasets: [
            {
              label: "Altura",
              data: selectedCoaster.map(coaster => coaster.height),
              borderColor: styles.color.solids[0]
            }
          ]
        },
        options: {
          legend: {
            display: false
          }
        }
      };
    },
    dataChart4() {
      return {
        type: "doughnut",
        data: {
          labels: [
            "Propulsada",
            "Hiper Montaña",
            "Giga Montaña",
            "Inversión",
            "Sentado"
          ],
          datasets: [
            {
              data: [
                this.coaster.filter(
                  coaster => coaster.model === "Accelerator Coaster"
                ).length,
                this.coaster.filter(
                  coaster => coaster.model === "Hyper Coaster"
                ).length,
                this.coaster.filter(coaster => coaster.model === "Giga Coaster")
                  .length,
                this.coaster.filter(
                  coaster => coaster.model === "Multi Inversion Coaster"
                ).length,
                this.coaster.filter(
                  coaster => coaster.model === "Sitting Coaster"
                ).length
              ],
              borderColor: styles.color.solids,
              backgroundColor: styles.color.alphas
            }
          ]
        },
        options: {
          legend: {
            position: "right"
          }
        }
      };
    },
    dataChart5() {
      const selectedCoaster = this.coaster.filter(coaster => coaster.gForce);
      return {
        type: "bar",
        data: {
          labels: selectedCoaster.map(coaster => coaster.name),
          datasets: [
            {
              data: selectedCoaster.map(coaster => coaster.gForce),
              borderColor: styles.color.solids,
              backgroundColor: styles.color.alphas
            }
          ]
        },
        options: {
          legend: {
            display: false
          },
          scales: {
            yAxes: [
              {
                gridLines: {
                  display: false
                },
                ticks: {
                  display: true
                }
              }
            ]
          }
        }
      };
    },
    dataChart6() {
      return {
        type: "line",
        data: {
          labels: [
            "1995-1997",
            "1998-2000",
            "2001-2003",
            "2004-2006",
            "2007-2009",
            "2010-2012",
            "2013-2015",
            "2016-2018",
            "2019-2021"
          ],
          datasets: [
            {
              label: "Montañas creadas",
              data: [
                this.coaster.filter(
                  coaster => coaster.year >= 1995 && coaster.year <= 1997
                ).length,
                this.coaster.filter(
                  coaster => coaster.year >= 1998 && coaster.year <= 2000
                ).length,
                this.coaster.filter(
                  coaster => coaster.year >= 2001 && coaster.year <= 2003
                ).length,
                this.coaster.filter(
                  coaster => coaster.year >= 2004 && coaster.year <= 2006
                ).length,
                this.coaster.filter(
                  coaster => coaster.year >= 2007 && coaster.year <= 2009
                ).length,
                this.coaster.filter(
                  coaster => coaster.year >= 2010 && coaster.year <= 2012
                ).length,
                this.coaster.filter(
                  coaster => coaster.year >= 2013 && coaster.year <= 2015
                ).length,
                this.coaster.filter(
                  coaster => coaster.year >= 2016 && coaster.year <= 2018
                ).length,
                this.coaster.filter(
                  coaster => coaster.year >= 2019 && coaster.year <= 2021
                ).length
              ],
              borderColor: styles.color.solids[3],
              backgroundColor: styles.color.alphas[3]
            },
            {
              type: "bar",
              label: "Aceleración",
              data: [
                this.coaster.filter(
                  coaster =>
                    coaster.year >= 1995 &&
                    coaster.year <= 1997 &&
                    coaster.model === "Hyper Coaster"
                ).length,
                this.coaster.filter(
                  coaster =>
                    coaster.year >= 1998 &&
                    coaster.year <= 2000 &&
                    coaster.model === "Hyper Coaster"
                ).length,
                this.coaster.filter(
                  coaster =>
                    coaster.year >= 2001 &&
                    coaster.year <= 2003 &&
                    coaster.model === "Hyper Coaster"
                ).length,
                this.coaster.filter(
                  coaster =>
                    coaster.year >= 2004 &&
                    coaster.year <= 2006 &&
                    coaster.model === "Hyper Coaster"
                ).length,
                this.coaster.filter(
                  coaster =>
                    coaster.year >= 2007 &&
                    coaster.year <= 2009 &&
                    coaster.model === "Hyper Coaster"
                ).length,
                this.coaster.filter(
                  coaster =>
                    coaster.year >= 2010 &&
                    coaster.year <= 2012 &&
                    coaster.model === "Hyper Coaster"
                ).length,
                this.coaster.filter(
                  coaster =>
                    coaster.year >= 2013 &&
                    coaster.year <= 2015 &&
                    coaster.model === "Hyper Coaster"
                ).length,
                this.coaster.filter(
                  coaster =>
                    coaster.year >= 2016 &&
                    coaster.year <= 2018 &&
                    coaster.model === "Hyper Coaster"
                ).length,
                this.coaster.filter(
                  coaster =>
                    coaster.year >= 2019 &&
                    coaster.year <= 2021 &&
                    coaster.model === "Hyper Coaster"
                ).length
              ],
              borderColor: styles.color.solids[5],
              backgroundColor: styles.color.alphas[5],
              barPercentage: 0.5
            }
          ]
        },
        options: {
          maintainAspectRatio: false,
          scaleFontColor: "#fff",
          scales: {
            yAxes: [
              {
                ticks: {
                  display: true
                }
              }
            ],
            xAxes: [
              {
                ticks: {
                  display: true
                }
              }
            ]
          }
        }
      };
    }
  },
  created() {
    this.getCoaster();
  }
};
</script>
<style scoped lang="scss">
* {
  background: #181f38;
  font-family: "Poppins", monospace;
  color: white;
  letter-spacing: 1px;
  font-weight: 300;
  padding: 1rem;
}
section {
  margin-bottom: 40px;
}
h1 {
  font-size: 3em;
  font-weight: 100;
}
h2 {
  margin-bottom: 29px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
  padding-bottom: 15px;
  font-weight: 200;
}
hr {
  border-color: white;
  // margin-bottom: 50px;
  padding: 0;
}

/* grid */

[class*="cols"] {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
}
.cols-3 > * {
  width: 33.33%;
  margin: 10px;
}
.cols-2 > * {
  width: 50%;
  margin: 10px;
}
.cols-1 > * {
  width: 100%;
  margin: 10px;
}
#chart6 {
  max-height: 300px;
}
</style>
