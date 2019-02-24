<template>
  <div>Valid Component</div>
</template>

<script lang="ts">
import { Component, Vue } from "nuxt-property-decorator";
import { Validation } from "vuelidate";
import { required, minLength, sameAs, helpers } from "vuelidate/lib/validators";

// custom validator
const mustBeCool = value => value.indexOf("cool") >= 0;

const mustBeCool2 = value => !helpers.req(value) || value.indexOf("cool") >= 0;

const contains = param => value =>
  !helpers.req(value) || value.indexOf(param) >= 0;

const mustBeCool3 = helpers.withParams(
  { type: "mustBeCool" },
  value => !helpers.req(value) || value.indexOf("cool") >= 0
);

@Component({
  validations: {
    password: {
      required,
      minLength: minLength(6)
    },
    repeatPassword: {
      sameAsPassword: sameAs("password")
    },
    form: {
      nestedA: {
        required
      },
      nestedB: {
        required
      }
    },
    flatA: { required },
    flatB: { required },
    forGroup: {
      nested: { required }
    },
    validationGroup: ["flatA", "flatB", "forGroup.nested"],
    people: {
      required,
      minLength: minLength(3),
      $each: {
        name: {
          required,
          minLength: minLength(2)
        }
      }
    },
    username: {
      required,
      isUnique(value) {
        // standalone validator ideally should not assume a field is required
        if (value === "") return true;

        // simulate async call, fail for all logins with even length
        return new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve(typeof value === "string" && value.length % 2 !== 0);
          }, 350 + Math.random() * 300);
        });
      }
    },
    myField: {
      required,
      mustBeCool,
      mustBeCool2,
      containsA: contains("a"),
      mustBeCool3
    }
  }
})
export default class ValidComponent extends Vue {
  password: string = "";
  repeatPassword: string = "";

  form = {
    nestedA: "A",
    nestedB: "B"
  };

  flatA: string = "";
  flatB: string = "";
  forGroup = {
    nested: ""
  };

  people: Array<string> = ["Pierre", "Paul", "Jacques"];

  username: string = "";

  myField: string = "";

  touchMap = new WeakMap();
  delayTouch($v: Validation) {
    $v.$reset();
    if (this.touchMap.has($v)) {
      clearTimeout(this.touchMap.get($v));
    }
    this.touchMap.set($v, setTimeout($v.$touch, 1000));
  }

  accessValidatorParams() {
    console.log(this.$v.$params.username);
    console.log(this.$v.username.$params);
  }

  accessGroups() {
    console.log(this.$v.validationGroup);
  }

  accessDollarV() {
    console.log(this.$v.$invalid);
  }

  hasDescription = false;
  validations() {
    if (!this.hasDescription) {
      return {
        name: {
          required
        }
      };
    } else {
      return {
        name: {
          required
        },
        description: {
          required
        }
      };
    }
  }
  get isRepoValid() {
    return !this.$v.$invalid;
  }
}
</script>