# do_action in Vue.js
Vue.js design pattern to mimic WordPress' do_action

This example is based on the file structure of [wePOS](https://github.com/weDevsOfficial/wepos) plugin. 

#### Bootstrap.js
```javascript
// Added at the bottom of Bootstrap.js
// wepos.hooks comes from the @wordpress/hooks package 

wepos.addFilter = (hookName, namespace, component) => {
    wepos.hooks.addFilter(hookName, namespace, (components) => {
        components.push(component);
        return components;
    } );
};
```

#### Anyfile.js
```javascript
// This snippets must be included AFTER Bootstrap.js and BEFORE rendering Home.vue.
// For example this could be in frontend/main.js

import HomeSampleListOne from 'frontend/components/HomeSampleListOne.vue';
import HomeSampleListTwo from 'frontend/components/HomeSampleListTwo.vue';
import HomeBeforeItemsWrapper from 'frontend/components/HomeBeforeItemsWrapper.vue';

// the follwing `weposHomeSampleList` and `weposHomeBeforeItemsWrapper` hooknames are
// used in $data properties in Home.vue
wepos.addFilter('weposHomeSampleList', 'weposHome', HomeSampleListOne);
wepos.addFilter('weposHomeSampleList', 'weposHome', HomeSampleListTwo);
wepos.addFilter('weposHomeBeforeItemsWrapper', 'weposHome', HomeBeforeItemsWrapper);
```

#### Home.vue
```html
<template>
    <div id="wepos-main" v-cloak v-hotkey="hotkeys">
        ...
        <ul>
            <li>One</li>
            <li>Two</li>
            <li>Three</li>

            <component
                v-for="(component, index) in sampleLists"
                :key="index"
                :is="component"
            />
        </ul>        

        <component
            v-for="(component, index) in beforeItemsWrapper"
            :key="index"
            :is="component"
            :products="getFilteredProduct"
        />        
        ...
    </div>
</template>

<scripts>
    export default {
        ...
        data() {
            return {
                // Add an empty array so that, by default Vue has nothing to render
                sampleLists: wepos.hooks.applyFilters( 'weposHomeSampleList', [] ),
                beforeItemsWrapper: wepos.hooks.applyFilters( 'weposHomeBeforeItemsWrapper', [] ),
            };
        },
        ...
    };
</scripts>
```

#### HomeSampleListOne.vue
```html
<template>
    <li>From HomeSampleListOne.vue</li>
</template>

<script>
    export default {};
</script>
```

#### HomeSampleListTwo.vue
```html
<template>
    <li>From HomeSampleListTwo.vue</li>
</template>

<script>
    export default {};
</script>
```

#### HomeBeforeItemsWrapper.vue
```html
<template>
    <div>
        component with div wrapper root
        <pre>{{ products }}</pre>
    </div>
</template>

<script>
    export default {
        props: {
            products: {
                type: Array,
                required: true
            }
        }
    };
</script>
```
