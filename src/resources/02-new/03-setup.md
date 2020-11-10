# Setup Simplified

With the new composition api the setup method provides a more functional approach to writing components, despite this, setup is not actually necessary as the options api is still 100% compatible with vue 3.

Each of these sections all accomplish the same thing. It is pretty much syntactic sugar so pick the style you like best.

__Options API__

``` vue
<script>
import { AppState } from '../../AppState'
import { AuthService } from '../../services/AuthService'

export default {
  computed:{
    user(){ return AppState.user) },
    profile(){ return AppState.profile) }
  }
  methods:{
    logout(){ AuthService.logout() },
    login(){ AuthService.loginWithRedirect() }
  }
}
</script>
```

__Mixed Setup API__

``` vue
<script>
import { computed } from 'vue'
import { AppState } from '../../AppState'
import { AuthService } from '../../services/AuthService'

export default {
  setup(props, { emit }){
    // the return object from setup will be accessible in the template
    return {
      user: computed(() => AppState.user),
      profile: computed(() => AppState.profile)  
    }
  }
  methods:{
    logout(){ AuthService.logout() },
    login(){ AuthService.loginWithRedirect() }
  }
}
</script>
```

__Setup Method__

``` vue
<script>
import { computed } from 'vue'
import { AppState } from '../../AppState'
import { AuthService } from '../../services/AuthService'

export default {
  setup(props, { emit }){
    // the return object from setup will be accessible in the template
    return {
      user: computed(() => AppState.user),
      profile: computed(() => AppState.profile) , 
      logout(){ AuthService.logout() },
      login(){ AuthService.loginWithRedirect() }
    }
  }
}
</script>
```

__Setup Attribute Only__

``` vue
<script setup={props, { emit } }>
import { computed } from 'vue'
import { AppState } from '../../AppState'
import { AuthService } from '../../services/AuthService'

// Anything exported is available for use by the template
export const logout = () => AuthService.logout()
export const login = () => AuthService.loginWithRedirect()

export const user = computed(() => AppState.user)
export const profile = computed(() => AppState.profile)

</script>
```
