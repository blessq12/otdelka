<script>
import { object, string } from 'yup';
import { useToast } from 'vue-toast-notification';
import 'vue-toast-notification/dist/theme-sugar.css';

import company from './data/company.js';
import reviews from './data/reviews.js';
import services from './data/services.js';
import advantages from './data/advantages.js';
import { config } from './config.js';

const toast = useToast({
  position: 'top-right',
  timeout: 5000,
  closeOnClick: true,
  pauseOnFocusLoss: true,
  pauseOnHover: true,
  draggable: true,
  draggablePercent: 0.6,
  showCloseButtonOnHover: false,
});

export default {
  name: 'App',
  data() {
    return {
      company,
      reviews,
      services,
      advantages,
      config,
      isMenuOpen: false,
      form: {
        name: '',
        phone: '',
        message: ''
      },
      formSchema: object().shape({
        name: string().required('Пожалуйста, введите ваше имя').min(3, 'Имя должно содержать минимум 3 символа'),
        phone: string().required('Пожалуйста, введите номер телефона').min(18, 'Номер телефона должен содержать минимум 10 символов'),
        message: string().required('Пожалуйста, введите сообщение').min(10, 'Сообщение должно содержать минимум 10 символов')
      }),
      navigation: [
        { name: 'Услуги', href: '#services' },
        { name: 'Преимущества', href: '#advantages' },
        { name: 'Отзывы', href: '#reviews' },
        { name: 'Контакты', href: '#contact' }
      ],
      maska: '+7 (###) ###-##-##'
    };
  },
  mounted() {
    // toast.success('Привет, мир!')
    // console.log(config)
  },
  methods: {
    toggleMenu() {
      this.isMenuOpen = !this.isMenuOpen;
    },
    submitForm() {
      this.formSchema.validate(this.form, { abortEarly: false })
        .then(() => {
          let message = "Заявка с сайта отделки!%0A";
          message += `Имя: ${this.form.name}%0A`;
          message += `Телефон: ${this.form.phone}%0A`;
          message += `Сообщение: ${this.form.message}%0A`;

          let telegramLink = `https://api.telegram.org/bot${config.telegram.token}/sendMessage?chat_id=${config.telegram.chat_id}&text=${message}`;

          fetch(telegramLink)
            .then(() => {
              toast.success('Сообщение отправлено! Мы свяжемся с вами в ближайшее время!');
              this.form.name = '';
              this.form.phone = '';
              this.form.message = '';
            })
            .catch((err) => console.log(err));
        })
        .catch((error) => {
          toast.error(error.errors[0]);
        });
    }
  }
};
</script>

<template>
  <!-- Header -->
  <header class="sticky top-0 bg-white/95 backdrop-blur-sm z-50 py-4 shadow-sm">
    <nav class="container">
      <div class="flex items-center justify-between">
        <a href="#" class="flex items-center gap-2">
          <img src="/images/logo.png" alt="Logo" class="h-8 w-auto">
          <span class="text-xl font-bold">{{ company.name }}</span>
        </a>
        <div class="hidden md:flex items-center gap-8">
          <a v-for="item in navigation" 
             :key="item.name"
             :href="item.href"
             class="text-gray-700 hover:text-primary transition-colors">
            {{ item.name }}
          </a>
          <a href="#cta" class="btn btn-primary">
            Оставить заявку
          </a>
        </div>
        <button @click="toggleMenu" class="lg:hidden p-2 -m-2">
          <span v-if="!isMenuOpen" class="mdi mdi-menu text-2xl"></span>
          <span v-else class="mdi mdi-close text-2xl"></span>
        </button>
      </div>
    </nav>
    <!-- Mobile Menu -->
    <Transition
      enter-active-class="transition duration-200 ease-out"
      enter-from-class="opacity-0"
      enter-to-class="opacity-100"
      leave-active-class="transition duration-200 ease-in"
      leave-from-class="opacity-100"
      leave-to-class="opacity-0"
    >
      <div v-if="isMenuOpen" class="fixed inset-0 bg-black/50 backdrop-blur-sm z-40 lg:hidden" style="height: 100vh;">
        <Transition
          enter-active-class="transition duration-300 ease-out"
          enter-from-class="translate-x-full"
          enter-to-class="translate-x-0"
          leave-active-class="transition duration-300 ease-in"
          leave-from-class="translate-x-0"
          leave-to-class="translate-x-full"
        >
          <div v-show="isMenuOpen" class="fixed inset-y-0 right-0 w-full max-w-sm bg-white shadow-lg px-6 py-6 overflow-y-auto">
            <div class="flex items-center justify-between mb-8">
              <a href="#" class="flex items-center gap-2">
                <img src="/images/logo.png" alt="Logo" class="h-8 w-auto">
                <span class="text-xl font-bold">{{ company.name }}</span>
              </a>
              <button @click="toggleMenu" class="p-2 -m-2">
                <span class="mdi mdi-close text-2xl"></span>
              </button>
            </div>
            <nav class="space-y-6">
              <div>
                <ul class="space-y-3">
                  <li v-for="item in navigation" :key="item.name">
                    <a :href="item.href" 
                       class="block text-lg font-medium text-gray-900 hover:text-primary"
                       @click="toggleMenu">
                      {{ item.name }}
                    </a>
                  </li>
                </ul>
              </div>
              <div>
                <h3 class="text-sm font-semibold text-gray-900 uppercase">Контакты</h3>
                <div class="mt-3 space-y-2">
                  <a :href="'tel:' + company.contacts.phone" class="flex items-center gap-2 text-gray-600 hover:text-primary">
                    <span class="mdi mdi-phone text-lg"></span>
                    <span>{{ company.contacts.phone }}</span>
                  </a>
                  <a :href="'mailto:' + company.contacts.email" class="flex items-center gap-2 text-gray-600 hover:text-primary">
                    <span class="mdi mdi-email text-lg"></span>
                    <span>{{ company.contacts.email }}</span>
                  </a>
                  <div class="flex items-center gap-2 text-gray-600">
                    <span class="mdi mdi-map-marker text-lg"></span>
                    <span>{{ company.contacts.address }}</span>
                  </div>
                  <div class="flex items-center gap-2 text-gray-600">
                    <span class="mdi mdi-clock text-lg"></span>
                    <span>{{ company.contacts.workingHours }}</span>
                  </div>
                </div>
              </div>
            </nav>
            <div class="mt-8">
              <a :href="'tel:' + company.contacts.phone" 
                 @click="toggleMenu"
                 class="btn btn-primary w-full">
                Позвонить нам
              </a>
            </div>
            <div class="mt-auto pt-6 text-sm text-gray-500">
              <p>&copy; {{ new Date().getFullYear() }} ООО «Строительная Компания ФорИнтерСтрой». Все права защищены. ИНН: 7024029308 ОГРН: 1087024001800</p>
            </div>
          </div>
        </Transition>
      </div>
    </Transition>
  </header>

  <!-- Hero Section -->
  <section class="hero-section">
    <div class="container relative">
      <div class="hero-content max-w-3xl">
        <h1 class="text-4xl sm:text-6xl font-bold mb-6">
          <span class="text-white">Качественный ремонт</span><br>
          <span class="text-white">для вашего дома</span>
        </h1>
        <p class="text-xl text-gray-200 mb-8">
          Мы специализируемся на качественном ремонте и отделке помещений любой сложности. Наша команда профессионалов имеет более 10 лет опыта в сфере ремонтных работ.
        </p>
        <div class="flex flex-col sm:flex-row gap-4">
          <a href="#cta" class="btn btn-primary">
            Оставить заявку
          </a>
          <a href="#services" class="btn btn-outline-white">
            Наши услуги
          </a>
        </div>
      </div>
    </div>
  </section>

  <!-- Advantages Section -->
  <section id="advantages" class="section bg-gray-50">
    <div class="container">
      <div class="section-header">
        <h2 class="section-title">Почему выбирают нас</h2>
        <p class="section-subtitle">
          Нам доверяют ремонт уже более 15 лет
        </p>
      </div>
      <div class="grid md:grid-cols-2 lg:grid-cols-3 gap-6">
        <div v-for="advantage in advantages" 
             :key="advantage.title"
             class="bg-white rounded-xl p-6 shadow-lg hover:shadow-xl transition-all duration-300 transform hover:-translate-y-1">
          <div class="flex items-start gap-4">
            <div class="w-12 h-12 rounded-lg bg-primary/10 flex items-center justify-center flex-shrink-0">
              <span :class="advantage.icon" class="text-2xl text-primary"></span>
            </div>
            <div>
              <h3 class="text-xl font-bold mb-2">{{ advantage.title }}</h3>
              <p class="text-gray-600">{{ advantage.description }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>

  <!-- Services Section -->
  <section id="services" class="section">
    <div class="container">
      <div class="section-header">
        <h2 class="section-title">Наши услуги</h2>
        <p class="section-subtitle">
          Полный спектр услуг по ремонту и отделке
        </p>
      </div>
      <div class="grid md:grid-cols-3 gap-8">
        <div v-for="service in services" 
             :key="service.id"
             class="service-card group">
          <div class="w-12 h-12 rounded-full bg-primary/10 flex items-center justify-center mb-4">
            <span :class="service.icon" class="text-2xl text-primary"></span>
          </div>
          <h3 class="text-xl font-semibold mb-2">{{ service.title }}</h3>
          <p class="text-gray-600">{{ service.description }}</p>
          <ul class="mt-4 space-y-2">
            <li v-for="feature in service.features"
                :key="feature"
                class="flex items-center gap-2 text-sm text-gray-600">
              <span class="mdi mdi-check text-primary"></span>
              <span>{{ feature }}</span>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </section>

  <!-- CTA Section -->
  <section id="cta" class="section bg-primary text-white">
    <div class="container">
      <div class="grid md:grid-cols-2 gap-12 items-center">
        <div>
          <h2 class="text-3xl sm:text-4xl font-bold mb-4">
            Получите бесплатную консультацию
          </h2>
          <p class="text-lg opacity-90 mb-8">
            Оставьте заявку прямо сейчас и получите:
          </p>
          <ul class="space-y-4">
            <li v-for="(advantage, index) in company.advantages"
                :key="index"
                class="flex items-center gap-3">
              <span class="mdi mdi-check text-2xl text-white"></span>
              <span>{{ advantage }}</span>
            </li>
          </ul>
        </div>
        <form @submit.prevent="submitForm" class="bg-white text-gray-900 rounded-xl p-6 shadow-xl">
          <div class="space-y-4">
            <div class="form-group">
              <label for="name" class="form-label">Ваше имя</label>
              <input 
                id="name"
                v-model="form.name"
                type="text"
                class="form-input"
                placeholder="Иван Иванов"
              >
            </div>
            <div class="form-group">
              <label for="phone" class="form-label">Телефон</label>
              <input 
                id="phone"
                v-model="form.phone"
                v-maska
                :data-maska="maska"
                type="tel"
                class="form-input"
                placeholder="+7 (___) ___-__-__"
              />
            </div>
            <div class="form-group">
              <label for="message" class="form-label">Сообщение</label>
              <textarea 
                id="message"
                v-model="form.message"
                rows="4"
                class="form-input resize-none"
                placeholder="Опишите ваш проект или задайте вопрос"
              ></textarea>
     
            </div>
            <button type="submit" class="btn btn-primary w-full">
              Отправить заявку
            </button>
            <p class="text-sm text-gray-500 text-center">
              Нажимая кнопку, вы соглашаетесь с политикой конфиденциальности
            </p>
          </div>
        </form>
      </div>
    </div>
  </section>

  <!-- Reviews Section -->
  <section id="reviews" class="section">
    <div class="container">
      <div class="section-header">
        <h2 class="section-title">Отзывы клиентов</h2>
        <p class="section-subtitle">
          Что говорят о нас наши клиенты
        </p>
      </div>
      <div class="grid md:grid-cols-3 gap-8">
        <div v-for="review in reviews" 
             :key="review.id"
             class="review-card">
          <div class="flex items-center gap-4 mb-4">
            <div class="w-12 h-12 rounded-full bg-primary/10 flex items-center justify-center">
              <span class="mdi mdi-account text-2xl text-primary"></span>
            </div>
            <div>
              <h4 class="font-semibold">{{ review.name }}</h4>
              <p class="text-sm text-gray-500">{{ new Date(review.date).toLocaleDateString('ru-RU') }}</p>
            </div>
          </div>
          <div class="flex gap-1 mb-3">
            <span 
              v-for="n in 5" 
              :key="n"
              :class="[
                'mdi', 
                n <= review.rating ? 'mdi-star text-yellow-400' : 'mdi-star-outline text-gray-200'
              ]"
              class="text-lg"
            />
          </div>
          <p class="text-gray-600">{{ review.text }}</p>
        </div>
      </div>
    </div>
  </section>

  <!-- Contact Section -->
  <section id="contact" class="py-24 bg-gray-50">
    <div class="container">
      <div class="grid lg:grid-cols-2 gap-12">
        <div>
          <h2 class="text-4xl font-bold mb-8">Контакты</h2>
          <div class="space-y-6">
            <div class="flex items-start gap-4">
              <div class="w-12 h-12 rounded-lg bg-primary/10 p-2 flex items-center justify-center">
                <span class="mdi mdi-map-marker text-2xl text-primary"></span>
              </div>
              <div>
                <h3 class="text-xl font-bold mb-1">Адрес</h3>
                <p class="text-gray-600">
                  г. Томск, ул. Розы Люксембург, 102Ак2
                </p>
              </div>
            </div>
            <div class="flex items-start gap-4">
              <div class="w-12 h-12 rounded-lg bg-primary/10 p-2 flex items-center justify-center">
                <span class="mdi mdi-phone text-2xl text-primary"></span>
              </div>
              <div>
                <h3 class="text-xl font-bold mb-1">Телефон</h3>
                <a href="tel:+7" class="text-gray-600 hover:text-primary transition-colors">
                  {{ company.contacts.phone }}
                </a>
              </div>
            </div>
            <div class="flex items-start gap-4">
              <div class="w-12 h-12 rounded-lg bg-primary/10 p-2 flex items-center justify-center">
                <span class="mdi mdi-clock text-2xl text-primary"></span>
              </div>
              <div>
                <h3 class="text-xl font-bold mb-1">Режим работы</h3>
                <p class="text-gray-600">
                  Пн-Пт: 9:00 - 18:00<br>
                  Сб-Вс: По договоренности
                </p>
              </div>
            </div>
          </div>
        </div>
        <div class="map-container">
          <div class="map-attribution">
            <a href="https://yandex.ru/maps/67/tomsk/?utm_medium=mapframe&utm_source=maps">Томск</a> |
            <a href="https://yandex.ru/maps/67/tomsk/house/ulitsa_rozy_lyuksemburg_102ak2/bE0YfwNpTEUPQFtsfXlxdn5hYA==/?ll=84.949508%2C56.507224&utm_medium=mapframe&utm_source=maps&z=16">
              Улица Розы Люксембург, 102Ак2
            </a>
          </div>
          <iframe 
            src="https://yandex.ru/map-widget/v1/?ll=84.949508%2C56.507224&mode=search&ol=geo&ouri=ymapsbm1%3A%2F%2Fgeo%3Fdata%3DCgg1NzYzMTA5OBJM0KDQvtGB0YHQuNGPLCDQotC-0LzRgdC6LCDRg9C70LjRhtCwINCg0L7Qt9GLINCb0Y7QutGB0LXQvNCx0YPRgNCzLCAxMDLQkNC6MiIKDSbmqUIVZgdiQg%2C%2C&z=16"
            width="100%"
            height="100%"
            frameborder="0"
            allowfullscreen="true"
          ></iframe>
        </div>
      </div>
    </div>
  </section>

  <!-- Footer -->
  <footer class="bg-gray-900 text-white py-12">
    <div class="container">
      <div class="grid md:grid-cols-3 gap-8">
        <div>
          <h3 class="text-xl font-bold mb-4">{{ company.name }}</h3>
          <p class="text-gray-400">
            {{ company.description }}
          </p>
        </div>
        <div>
          <h4 class="text-lg font-semibold mb-4">Навигация</h4>
          <div class="space-y-2">
            <a v-for="item in navigation"
               :key="item.name"
               :href="item.href"
               class="block text-gray-400 hover:text-white transition-colors">
              {{ item.name }}
            </a>
          </div>
        </div>
        <div>
          <h4 class="text-lg font-semibold mb-4">Контакты</h4>
          <div class="space-y-2 text-gray-400">
            <p>{{ company.contacts.phone }}</p>
            <p>{{ company.contacts.email }}</p>
            <p>{{ company.contacts.address }}</p>
          </div>
        </div>
      </div>
      <div class="border-t border-gray-800 mt-12 pt-8 text-center text-gray-400">
        <p>&copy; {{ new Date().getFullYear() }} ООО «Строительная Компания ФорИнтерСтрой». Все права защищены. ИНН: 7024029308 ОГРН: 1087024001800</p>
      </div>
    </div>
  </footer>
</template>
<style scoped>
/* Ваши стили */
</style>

