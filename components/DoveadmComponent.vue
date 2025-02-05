<script setup>
import { data } from '../lib/data/doveadm.data.js'

/* Properties for this component:
 * 'plugin' (string): Filter by the plugin property.
 * 'tag' (string): Filter by tag. Tag matches either plugin OR tag field.
 */
const props = defineProps(['plugin', 'tag'])

const d = Object.fromEntries(Object.entries(data.doveadm).filter(([k, v]) =>
	/* Filter entries by plugin or tag. */
	(!props.plugin && !props.tag) ||
	(props.plugin &&
	 (v.plugin && v.plugin == props.plugin)) ||
	(props.tag &&
	 ((v.plugin && v.plugin == props.tag) ||
	  (v.tags.includes(props.tag))))
).sort())
</script>

<style scoped>
.doveadmList article:first-of-type {
  border-top-width: 0;
}
.doveadmList article {
  border-top: 1px solid var(--vp-c-divider);
}
.doveadmList h3 {
  margin-top: 18px;
}
</style>

<template>
 <section class="doveadmList">
  <article v-for="(v, k) in d">
   <h3 :id="k" tabindex="-1">
    <code>{{ k }}</code>
    <a class="header-anchor" :href="'#' + k"></a>
   </h3>

   <table v-if="v.man_link || v.fields || v.added || v.changed || v.deprecated || v.removed">
    <tbody>
     <tr v-if="v.man_link">
      <th style="text-align: right;">Man Page</th>
      <td v-html="v.man_link" />
     </tr>

     <tr v-if="v.added || v.changed || v.deprecated || v.removed">
      <th style="text-align: right;">Changes</th>
      <td>
       <ul>
        <li v-if="v.added" v-for="elem in v.added">
         <span v-html="elem.version" />
         <span v-html="elem.text" />
        </li>
        <li v-if="v.changed" v-for="elem in v.changed">
         <span v-html="elem.version" />
         <span v-html="elem.text" />
        </li>
        <li v-if="v.deprecated" v-for="elem in v.deprecated">
         <span v-html="elem.version" />
         <span v-html="elem.text" />
        </li>
        <li v-if="v.removed" v-for="elem in v.removed">
         <span v-html="elem.version" />
         <span v-html="elem.text" />
        </li>
       </ul>
      </td>
    </tr>

     <tr v-if="v.fields">
      <th style="text-align: right;">Return Values</th>
      <td>
       <table>
        <thead>
         <tr>
          <th>Key</th>
          <th>Value</th>
         </tr>
        </thead>
        <tbody>
         <tr v-for="elem in v.fields">
          <td><code>{{ elem.key }}</code></td>
          <td v-html="elem.value" />
         </tr>
        </tbody>
       </table>
      </td>
     </tr>
    </tbody>
   </table>

   <div v-if="v.text" v-html="v.text" />

   <div class="info custom-block">
    <p class="custom-block-title">CLI</p>
    <div>
     <ul>
      <li>Usage: <code>doveadm {{ v.usage }}</code></li>
     </ul>

     <table v-if="v.args">
      <thead>
       <tr>
        <th>Argument(s)</th>
        <th>Type</th>
        <th>Description</th>
       </tr>
      </thead>
      <tbody>
       <template v-for="elem in v.args">
        <tr>
         <td><code>{{ elem.flag }}</code></td>
         <td>{{ elem.type }}</td>
         <td v-html="elem.text" />
        </tr>
       </template>
      </tbody>
     </table>
    </div>
   </div>

   <details v-if="v.args" class="details custom-block">
    <summary v-html="data.http_api_link" />
    <div>
     <table>
      <thead>
       <tr>
        <th>Parameter</th>
        <th>Type</th>
        <th>Description</th>
       </tr>
      </thead>
      <tbody>
       <template v-for="elem in v.args">
        <tr>
         <td><code>{{ elem.param }}</code></td>
         <td>{{ elem.type }}</td>
         <td v-html="elem.text" />
        </tr>
       </template>
      </tbody>
     </table>
    </div>
   </details>

  </article>
 </section>
</template>
