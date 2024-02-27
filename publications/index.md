---
title: Publications
layout: academic
author_profile: true
---

<!-- {% raw %} -->
<div id="app">
    <div>
      <p><br></p>
      <p style="width:60%;">
          <Slider
            v-model="yearslider.value"
            v-bind="yearslider"
          ></Slider>
      </p>
      <p>Filter to papers meeting any of the following criteria:</p>
      <ul>
        <li class="checkboxlist">
        Type
        <label class="container">Empirical
            <input type="checkbox" v-model="show.empirical">
            <span class="checkmark"></span>
        </label>
        <label class="container">Methods
            <input type="checkbox" v-model="show.methods">
            <span class="checkmark"></span>
        </label>
        </li>
        <li class="checkboxlist">
            Authorship
            <label class="container">Fist
                <input type="checkbox" v-model="show.first">
                <span class="checkmark"></span>
            </label>
            <label class="container">Senior
                <input type="checkbox" v-model="show.last">
                <span class="checkmark"></span>
            </label>
        </li>
        <li class="checkboxlist">
            Status
            <label class="container">Published
                <input type="checkbox" v-model="show.published">
                <span class="checkmark"></span>
            </label>
            <label class="container">In preparation
                <input type="checkbox" v-model="show.inprep">
                <span class="checkmark"></span>
            </label>
        </li>
      </ul>
    </div>
    <div v-for="yr in [...new Set(publ.map(a => a.year))].sort().reverse()">
      <h2>{{ yr }}</h2>
      <ul class="publist">
        <div v-for="pub in publ.filter(a => (a.year === yr))">
          <li class="publist">{{ pub.text }}<span v-if="pub.preprint != ''"> &mdash; <a v-bind:href="pub.preprint">Preprint</a></span><span v-if="pub.article != ''"> &mdash; <a v-bind:href="pub.article">Article</a></span><span v-if="pub.thesis != ''"> &mdash; <a v-bind:href="pub.thesis">Thesis</a></span><span v-if="pub.datarepo != ''"> &mdash; <a v-bind:href="pub.datarepo">Data repository</a></span><span v-if="pub.rpackagename != ''"> &mdash; <a v-bind:href="pub.rpackagelink">{{ pub.rpackagename }}</a></span><span v-if="pub.webappname != ''"> &mdash; <a v-bind:href="pub.webapplink">{{ pub.webappname }}</a></span><span v-if="pub.doi != ''">&nbsp;<div data-badge-popover="bottom" style="display: inline-block;" data-badge-type="4" v-bind:data-doi="pub.doi" data-hide-no-mentions="true" class="altmetric-embed"></div></span></li>
        </div>
      </ul>
    </div>
</div>
<!-- {% endraw %} -->

<script>
// publication list
var p = [
        {% for ms in site.data.publications %}{
          "id": "{{ ms.id }}",
          "text": "{{ ms.text }}",
          "year": {{ ms.year }},
          "type": "{{ ms.type }}",
          "authorship": "{{ ms.authorship }}",
          "status": "{{ ms.status }}",
          "preprint": "{{ ms.preprint }}",
          "article": "{{ ms.article }}",
          "thesis": "{{ ms.thesis }}",
          "datarepo": "{{ ms.datarepo }}",
          "rpackagename": "{{ ms.rpackagename }}",
          "rpackagelink": "{{ ms.rpackagelink }}",
          "webappname": "{{ ms.webappname }}",
          "webapplink": "{{ ms.webapplink }}",
          "doi": "{{ ms.doi }}"
        }{% unless forloop.last %},{% endunless %}
      {% endfor %}];
// unique years
var yrs = [...new Set(p.map(a => a.year))].sort().reverse();
//vue app
const app = Vue.createApp({
  data: () => ({
    yearslider: {
        value: [Math.min(...yrs), Math.max(...yrs)],
        min: Math.min(...yrs),
        max: Math.max(...yrs),
    },
    pubs: p,
    allyears: yrs,
    show: {
        empirical: true,
        methods: true,
        first: true,
        last: true,
        published: true,
        inprep: true,
    },
  }),
  computed: {
    publ: function () {
        var x = [];
        for (i = 0; i < this.pubs.length; i++) {
            let add = false;
            // type
            if (this.show.empirical && this.pubs[i].type == "empirical")
                add = true;
            if (this.show.methods && this.pubs[i].type == "methods")
                add = true;
            // authorship
            if (this.show.first && this.pubs[i].authorship == "first")
                add = true;
            if (this.show.last && this.pubs[i].authorship == "last")
                add = true;
            // status
            if (this.show.published && this.pubs[i].status == "published")
                add = true;
            if (this.show.inprep && this.pubs[i].status != "published")
                add = true;
            if (add) {
                if (this.pubs[i].year < this.yearslider.value[0]) {
                    add = false;
                }
                if (this.pubs[i].year > this.yearslider.value[1]) {
                    add = false;
                }
            }
            if (add)
                x.push(this.pubs[i]);
        }
        return x
    }
  }
})
// slider component
app.component('Slider', VueformSlider)
app.mount('#app')
</script>
