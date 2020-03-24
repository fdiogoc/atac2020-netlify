<template>
  <section class="home">
    <div class="md:py-36 mx-auto flex flex-wrap flex-col md:flex-row items-center">
      <div class="flex flex-col w-full xl:w-5/5 justify-center lg:items-start overflow-y-hidden">
        <div v-html="$md.render(welcomeText)" class="home__welcome markdown" />

        <div class="flex flex-wrap">
          <div
            class=" w-full md:w-1/2 mb-12 xl:mb-0 rounded border-gray-200 border-2 bg-gray-100  flex-none"
          >
            <h4 v-if="isSignedUp">Obrigado, logo entraremos em contato.</h4>

            <form
              v-else
              @submit.prevent="handleSubmit"
              name="faleConosco"
              netlify
              class="flex items-center border-b border-b-2 border-blue-400 py-2"
            >
              <div class="flex flex-wrap mb-6">
                <div class="w-full px-3">
                  <div class="font-bold text-xl mb-2  text-gray-700">Fale conosco:</div>
                  <label class="block">
                    <span class="text-gray-700">Nome</span>
                    <input
                      class="appearance-none w-full text-gray-700 border py-3 px-4 mb-3 leading-tight focus:outline-none focus:bg-white rounded"
                      id="grid-first-name"
                      type="text"
                      placeholder="Nome"
                    />
                  </label>
                </div>
                <div class="w-full px-3">
                  <label class="block">
                    <span class="text-gray-700">Email</span>
                    <input
                      ref="emailInput"
                      v-model="form.email"
                      class="appearance-none mb-36 bg-transparent border w-full text-gray-700 py-3 px-4 mb-3 leading-tight focus:outline-none rounded"
                      type="text"
                      name="email"
                      placeholder="nome@email.com.br"
                      aria-label="Email"
                    />
                  </label>
                </div>
                <div class="w-full px-3">
                  <label class="block">
                    <span class="text-gray-700">Mensagem</span>
                    <textarea
                      class="form-textarea mt-1 block w-full focus:outline-none"
                      rows="3"
                      placeholder="Digite sua mensagem."
                    ></textarea>
                  </label>
                </div>
                <div class="w-full px-3">
                  <button
                    class="flex-shrink-0 bg-blue-500 hover:bg-blue-700 border-blue-500 hover:border-blue-700 text-sm border-4 text-white py-1 px-2 rounded mt-5 rounded"
                    type="submit"
                  >
                    Enviar
                  </button>
                </div>
              </div>
            </form>
          </div>
          <div class="w-full md:w-1/2 flex-none md:-mx-4 pb-20  md:px-6">
            <h2 class="text-base md:text-lg lg:text-xl xl:text-2xl">Comunicados</h2>
            <div v-for="(post, index) in posts" :key="index" class="w-full">
              <div class="post">
                <nuxt-link :to="`/comunicados/${post.slug}`">
                  <div class="p-2 bg-gray">
                    <h2 class="text-2xl mb-2">{{ post.title }}</h2>

                    <p class="text-base font-light">
                      {{ post.excerpt }}
                    </p>

                    <h6 class="text-blue-600 mt-4 font-medium">Ler Mais</h6>
                  </div>
                </nuxt-link>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import settings from '@/content/settings/general.json';

// Inside page components
this.$OneSignal.push(() => {
  this.$OneSignal.isPushNotificationsEnabled(isEnabled => {
    if (isEnabled) {
      console.log('Push notifications are enabled!');
    } else {
      console.log('Push notifications are not enabled yet.');
    }
  });
});

// Using window and array form
window.$OneSignal.push([
  'addListenerForNotificationOpened',
  data => {
    console.log('Received NotificationOpened:', data);
  },
]);

@Component({
  // Called to know which transition to apply
  transition() {
    return 'slide-left';
  },
})
export default class Home extends Vue {
  welcomeText = settings.welcomeText;

  get posts(): Post[] {
    return this.$store.state.posts;
  }

  isSignedUp = false;

  form = {
    email: '',
  };

  encode(data): string {
    return Object.keys(data)
      .map(key => `${encodeURIComponent(key)}=${encodeURIComponent(data[key])}`)
      .join('&');
  }

  validEmail(email): boolean {
    // eslint-disable-next-line
    const re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
    return re.test(email);
  }

  async handleSubmit(): Promise<void> {
    if (!this.validEmail(this.form.email)) {
      this.$refs.emailInput.focus();
      return;
    }

    try {
      await fetch('/', {
        method: 'POST',
        headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
        body: this.encode({ 'form-name': 'faleConosco', ...this.form }),
      });

      this.isSignedUp = true;
    } catch (error) {
      console.error(error);
    }
  }
}
</script>
