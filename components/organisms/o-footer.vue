<template>
  <footer class="o-footer">
    <SfFooter :column="5" :multiple="true">
      <SfFooterColumn
        v-for="linkGroup in links"
        :key="linkGroup.name"
        :title="$t(linkGroup.name)"
      >
        <SfList>
          <SfListItem v-for="link in linkGroup.children" :key="link.name">
            <router-link v-if="link.link" :to="localizedRoute(link.link)" exact>
              <SfMenuItem class="sf-footer__menu-item" :label="$t(link.name)" />
            </router-link>
            <SfMenuItem
              v-else-if="link.clickHandler"
              class="sf-footer__menu-item"
              :label="$t(link.name)"
              @click="link.clickHandler"
            />
          </SfListItem>
        </SfList>
      </SfFooterColumn>
      <SfFooterColumn :title="$t('Social')" class="social-column">
        <div class="social-icon">
          <a 
            v-for="item in social"
            :key="item.name"
            :href="item.url"
            target="_blank"
            class="social-icon__img"         
          >
            <img
              :src="'/assets/icons/' + item.name + '.svg'"
              class="social-icon__img"
            >
          </a>
        </div>
      </SfFooterColumn>
    </SfFooter>
    <ABackToTop bottom="20px" right="20px" visibleoffset="200" class="desktop-only" />
  </footer>
</template>

<script>
import { mapActions, mapGetters } from 'vuex';
import ABackToTop from 'theme/components/atoms/a-back-to-top';
import { SfFooter, SfList, SfMenuItem } from '@storefront-ui/vue';
import { ModalList } from 'theme/store/ui/modals'
import { getPathForStaticPage } from 'theme/helpers';
import config from 'config';
import { currentStoreView } from '@vue-storefront/core/lib/multistore';
import get from 'lodash-es/get';

export default {
  name: 'OFooter',
  components: {
    ABackToTop,
    SfFooter,
    SfList,
    SfMenuItem
  },
  data () {
    return {
      social: [
                {
                  name: 'facebook',
                  url: 'https://www.facebook.com/tuctuc.and.friends.mk/?ref=bookmarks'
                },
                {
                  name: 'instagram',
                  url: 'https://www.instagram.com/tuctuc_and_friends.mk/'
                }
              ]
    };
  },
  computed: {
    ...mapGetters('user', ['isLoggedIn']),
    multistoreEnabled () {
      return get(config, 'storeViews.multistore', false);
    },
    getVersionInfo () {
      return ''; //`v${process.env.__APPVERSION__} ${process.env.__BUILDTIME__}`;
    },
    currentLanguage () {
      const { i18n = config.i18n } = currentStoreView();
      return `${i18n.defaultCountry} / ${i18n.defaultLanguage} / ${i18n.currencyCode}`;
    },
    links () {
      return {
        orders: {
          name: 'Orders',
          children: [
            {
              name: 'My account',
              ...this.isLoggedIn
                ? { link: '/my-account' }
                : { clickHandler: () => this.openModal({ name: ModalList.Auth, payload: 'login' }) }
            },
            { 
              name: 'Delivery', 
              link: getPathForStaticPage('/delivery')
            }
          ]
        },
        help: {
          name: 'Help',
          children: [
            { 
              name: 'Size guide', 
              link: getPathForStaticPage('/size-guide')
            },
            { 
              name: 'Contact us', 
              link: getPathForStaticPage('/contact')
            }
          ]
        },
        about: {
          name: 'About us',
          children: [
            {
              name: 'About us',
              link: getPathForStaticPage('/about-us')
            },
            { name: 'Store locator', 
              link: getPathForStaticPage('/store-locator')
            }
          ]
        },
        others: {
          name: 'Others',
          children: [
            {
              name: 'Legal notice',
              link: getPathForStaticPage('/legal')
            },
            {
              name: 'Privacy policy',
              link: getPathForStaticPage('/privacy')
            }
          ]
        }
      };
    }
  },
  methods: {
    ...mapActions('ui', {
      openModal: 'openModal'
    }),
    showLanguageSwitcher () {
      this.openModal({ name: ModalList.LanguageSwitcher })
    }
  }
};
</script>

<style lang="scss" scoped>
@import "~@storefront-ui/shared/styles/helpers/breakpoints";

.o-footer {
  @include for-desktop {
    max-width: 1272px;
    margin: auto;
  }
  .sf-footer {
    --footer-width: auto;
  }
}
.social-column {
  flex-basis: auto;
}
.social-icon {
  padding: 20px 40px;
  @include for-desktop {
    padding: 6px 0;
  }
  &__img {
    height: 1.75rem;
    &:not(:last-child) {
      margin-right: 1.25rem;
    }
  }
}
</style>
