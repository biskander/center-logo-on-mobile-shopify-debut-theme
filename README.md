<h1>Center logo on mobile - Shopify - Debut Theme</h1>

<ul>
  <li>Centers logo on mobile</li>
  <li>Display's hamburger on left side</li>
  <li>Hides search icon - remove <code>small--hide</code> from line 141 to show search.</li>
</ul>

<h3>How to use</h3>

<p>Take a backup of your <code>header.liquid</code> file and replace with the code below:</p>

<pre>
{% if section.settings.message %}
  <style>
    {% if section.settings.logo != blank %}
      .site-header__logo-image {
        max-width: {{ section.settings.logo_max_width | escape }}px;
      }
    {% endif %}

    {% if section.settings.align_logo == 'left' %}
      .site-header__logo-image {
        margin: 0;
      }
    {% endif %}
  </style>
{% endif %}

<div data-section-id="{{ section.id }}" data-section-type="header-section">
  {% if section.settings.message %}
    {% if section.settings.home_page_only == false or template.name == 'index' %}
      <style>
        .announcement-bar {
          background-color: {{ section.settings.color_bg }};
        }

        .announcement-bar--link:hover {
          {% assign brightness = section.settings.color_bg | color_brightness %}

          {% if brightness <= 192 %}
            {% assign lightenAmount = 255 | minus: brightness | divided_by: 255 | times: 16 %}
            background-color: {{ section.settings.color_bg | color_lighten: lightenAmount }};
          {% else %}
            {% assign darkenAmount = 255 | divided_by: brightness | times: 8 %}
            background-color: {{ section.settings.color_bg | color_darken: darkenAmount }};
          {% endif %}
        }

        .announcement-bar__message {
          color: {{ section.settings.color_text }};
        }
      </style>

      {% if section.settings.message_link == blank %}
        <div class="announcement-bar">
      {% else %}
        <a href="{{ section.settings.message_link }}" class="announcement-bar announcement-bar--link">
      {% endif %}

        <p class="announcement-bar__message">{{ section.settings.message_text | escape }}</p>

      {% if section.settings.message_link == blank %}
        </div>
      {% else %}
        </a>
      {% endif %}

    {% endif %}
  {% endif %}

  <header class="site-header{% if section.settings.align_logo == 'left' %} border-bottom logo--left{% else %} logo--center{% endif %}" role="banner">
    <div class="grid grid--no-gutters grid--table site-header__mobile-nav">
      {% if section.settings.align_logo == 'center' %}
        {%- assign logo_classes = 'small--two-quarters medium-up--one-third medium-up--push-one-third logo-align--center' -%}
      {% else %}
        {%- assign logo_classes = 'medium-up--one-quarter logo-align--left' -%}
      {% endif %}
        
      <div class="grid__item small--one-quarter medium-up--hide">

      {% unless linklists[section.settings.main_linklist] == blank %}
            <button type="button" class="btn--link site-header__icon site-header__menu js-mobile-nav-toggle mobile-nav--open" aria-controls="MobileNav"  aria-expanded="false" aria-label="{{ 'layout.navigation.menu' | t }}">
              {% include 'icon-hamburger' %}
              {% include 'icon-close' %}
            </button>
      {% endunless %}
        </div>

      <div class="grid__item {{ logo_classes }}">
        {% comment %}
          Use the uploaded logo from theme settings if enabled.
          Site name gets precedence with H1 tag on homepage, div on other pages.
        {% endcomment %}
        {% if template.name == 'index' %}
          <h1 class="h2 site-header__logo" style="padding-left:0px">
        {% else %}
          <div class="h2 site-header__logo" style="padding-left:0px">
        {% endif %}
          {% if section.settings.logo %}
            {%- assign img_url = section.settings.logo | img_url: '1x1' | replace: '_1x1.', '_{width}x.' -%}
            <a href="/" class="site-header__logo-image{% if section.settings.align_logo == 'center' %} site-header__logo-image--centered{% endif %}">
              {% capture logo_alt %}{{ section.settings.logo.alt | default: shop.name }}{% endcapture %}
              <img class="lazyload js"
                   src="{{ section.settings.logo | img_url: '300x300' }}"
                   data-src="{{ img_url }}"
                   data-widths="[180, 360, 540, 720, 900, 1080, 1296, 1512, 1728, 2048]"
                   data-aspectratio="{{ section.settings.logo.aspect_ratio }}"
                   data-sizes="auto"
                   alt="{{ logo_alt | escape }}"
                   style="max-width: {{ section.settings.logo_max_width }}px">
              <noscript>
                {% capture image_size %}{{ section.settings.logo_max_width | escape }}x{% endcapture %}
                <img src="{{ section.settings.logo | img_url: image_size }}"
                     srcset="{{ section.settings.logo | img_url: image_size }} 1x, {{ section.settings.logo | img_url: image_size, scale: 2 }} 2x"
                     alt="{{ section.settings.logo.alt | default: shop.name }}"
                     style="max-width: {{ section.settings.logo_max_width }}px;">
              </noscript>
            </a>
          {% else %}
            <a class="site-header__logo-link" href="/">{{ shop.name }}</a>
          {% endif %}
        {% if template.name == 'index' %}
          </h1>
        {% else %}
          </div>
        {% endif %}
      </div>

      {% if section.settings.align_logo == 'left' %}
        <nav class="grid__item medium-up--one-half small--hide" id="AccessibleNav" role="navigation">
          {% include 'site-nav' %}
        </nav>
      {% endif %}

      <div class="grid__item small--one-quarter {% if section.settings.align_logo == 'left' %}medium-up--one-quarter{% else %}medium-up--one-third medium-up--push-one-third{% endif %}">
        <div class="site-header__icons-wrapper">
          <div class="site-header__search site-header__icon">
            {% include 'search-form' %}
          </div>

          <button type="button" class="small--hide btn--link site-header__icon site-header__search-toggle js-drawer-open-top">
            {% include 'icon-search' %}
            <span class="icon__fallback-text">{{ 'layout.navigation.search' | t }}</span>
          </button>

          {% if shop.customer_accounts_enabled %}
            {% if customer %}
              <a href="/account" class="site-header__icon site-header__account">
                {% include 'icon-login' %}
                <span class="icon__fallback-text">{{ 'layout.customer.account' | t }}</span>
              </a>
            {% else %}
              <a href="/account/login" class="site-header__icon site-header__account">
                {% include 'icon-login' %}
                <span class="icon__fallback-text">{{ 'layout.customer.log_in' | t }}</span>
              </a>
            {% endif %}
          {% endif %}
          

          <a href="/cart" class="site-header__icon site-header__cart">
            {% include 'icon-cart' %}
            <span class="icon__fallback-text">{{ 'layout.cart.title' | t }}</span>
            {% if cart.item_count > 0 %}
              <div id="CartCount" class="site-header__cart-count">
                <span>{{ cart.item_count }}</span>
                <span class="icon__fallback-text medium-up--hide">{{ 'layout.cart.items_count' | t: count: cart.item_count }}</span>
              </div>
            {% endif %}
          </a>

          {% if shop.enabled_currencies.size > 1 %}
            {% form 'currency', class: "currency-selector small--hide" %}
              <label for="CurrencySelector" class="visually-hidden">{{ 'general.currency.dropdown_label' | t }}</label>
              <div class="currency-selector__input-wrapper select-group">
                <select name="currency" id="CurrencySelector" class="currency-selector__dropdown" aria-describedby="a11y-refresh-page-message a11y-selection-message" data-currency-selector>
                  {% for currency in shop.enabled_currencies %}
                    <option value="{{currency.iso_code}}"{% if currency == cart.currency %} selected="true"{% endif %}>{{currency.iso_code}}</option>
                  {% endfor %}
                </select>
                {% include 'icon-chevron-down' %}
              </div>
            {% endform %}
          {% endif %}

        </div>

      </div>
    </div>

    <nav class="mobile-nav-wrapper medium-up--hide" role="navigation">
      <ul id="MobileNav" class="mobile-nav">
        {% for link in linklists[section.settings.main_linklist].links %}
          {%- assign outerLoopIndex = forloop.index -%}

          <li class="mobile-nav__item{% unless forloop.last %} border-bottom{% endunless %}">
            {% if link.links != blank %}
              {% capture child_list_handle %}{{ link.handle }}-{{ outerLoopIndex }}{% endcapture %}
              <button type="button" class="btn--link js-toggle-submenu mobile-nav__link{% if link.child_active %} mobile-nav__link--active{% endif %}" data-target="{{ child_list_handle }}" data-level="1" aria-expanded="false">
                <span class="mobile-nav__label">{{ link.title | escape }}</span>
                <div class="mobile-nav__icon">
                  {% include 'icon-chevron-right' %}
                </div>
              </button>
              <ul class="mobile-nav__dropdown" data-parent="{{ child_list_handle }}" data-level="2">
                <li class="visually-hidden" tabindex="-1" data-menu-title="2">
                  {{- link.title }} {{ 'layout.navigation.menu' | t -}}
                </li>
                <li class="mobile-nav__item border-bottom">
                  <div class="mobile-nav__table">
                    <div class="mobile-nav__table-cell mobile-nav__return">
                      <button class="btn--link js-toggle-submenu mobile-nav__return-btn" type="button" aria-expanded="true" aria-label="{{ link.title }}">
                        {% include 'icon-chevron-left' %}
                      </button>
                    </div>
                    <span class="mobile-nav__sublist-link mobile-nav__sublist-header mobile-nav__sublist-header--main-nav-parent">
                      <span class="mobile-nav__label">{{ link.title | escape }}</span>
                    </span>
                  </div>
                </li>

                {% for childlink in link.links %}
                  <li class="mobile-nav__item{% unless forloop.last %} border-bottom{% endunless %}">
                    {% if childlink.links != blank %}
                      {% capture grandchild_list_handle %}{{ childlink.handle }}-{{ outerLoopIndex }}-{{ forloop.index }}{% endcapture %}
                      <button type="button" class="btn--link js-toggle-submenu mobile-nav__link mobile-nav__sublist-link" data-target="{{ grandchild_list_handle }}" aria-expanded="false">
                        <span class="mobile-nav__label">{{ childlink.title | escape }}</span>
                        <div class="mobile-nav__icon">
                          {% include 'icon-chevron-right' %}
                        </div>
                      </button>
                      <ul class="mobile-nav__dropdown" data-parent="{{ grandchild_list_handle }}" data-level="3">
                        <li class="visually-hidden" tabindex="-1" data-menu-title="3">
                          {{- childlink.title }} {{ 'layout.navigation.menu' | t -}}
                        </li>
                        <li class="mobile-nav__item border-bottom">
                          <div class="mobile-nav__table">
                            <div class="mobile-nav__table-cell mobile-nav__return">
                              <button type="button" class="btn--link js-toggle-submenu mobile-nav__return-btn" data-target="{{ child_list_handle }}" aria-expanded="true" aria-label="{{ childlink.title }}">
                                {% include 'icon-chevron-left' %}
                              </button>
                            </div>
                            <a href="{{ childlink.url }}"
                              class="mobile-nav__sublist-link mobile-nav__sublist-header"
                              {% if childlink.current %} aria-current="page"{% endif %}
                            >
                              <span class="mobile-nav__label">{{ childlink.title | escape }}</span>
                            </a>
                          </div>
                        </li>
                        {% for grandchildlink in childlink.links %}
                          <li class="mobile-nav__item{% unless forloop.last %} border-bottom{% endunless %}">
                            <a href="{{ grandchildlink.url }}"
                              class="mobile-nav__sublist-link"
                              {% if grandchildlink.current %} aria-current="page"{% endif %}
                            >
                              <span class="mobile-nav__label">{{ grandchildlink.title | escape }}</span>
                            </a>
                          </li>
                        {% endfor %}
                      </ul>
                    {% else %}
                      <a href="{{ childlink.url }}"
                        class="mobile-nav__sublist-link"
                        {% if childlink.current %} aria-current="page"{% endif %}
                      >
                        <span class="mobile-nav__label">{{ childlink.title | escape }}</span>
                      </a>
                    {% endif %}
                  </li>
                {% endfor %}
              </ul>
            {% else %}
              <a href="{{ link.url }}"
                class="mobile-nav__link{% if link.active %} mobile-nav__link--active{% endif %}"
                {% if link.current %} aria-current="page"{% endif %}
              >
                <span class="mobile-nav__label">{{ link.title | escape }}</span>
              </a>
            {% endif %}
          </li>
        {% endfor %}
        {% if shop.enabled_currencies.size > 1 %}
          <li class="mobile-nav__item border-top">
            {% form 'currency', class: "currency-selector" %}
              <label for="CurrencySelectorMobile" class="currency-selector__label">{{ 'general.currency.dropdown_label' | t }}</label>
              <div class="currency-selector__input-wrapper select-group">
                <select name="currency" id="CurrencySelectorMobile" class="currency-selector__dropdown" aria-describedby="a11y-refresh-page-message a11y-selection-message" data-currency-selector>
                  {% for currency in shop.enabled_currencies %}
                    <option value="{{currency.iso_code}}"{% if currency == cart.currency %} selected="true"{% endif %}>{{currency.iso_code}}</option>
                  {% endfor %}
                </select>
                {% include 'icon-chevron-down' %}
              </div>
            {% endform %}
          </li>
        {% endif %}
      </ul>
    </nav>
  </header>

  {% if section.settings.align_logo == 'center' %}
  <nav class="small--hide border-bottom" id="AccessibleNav" role="navigation">
    {% include 'site-nav', nav_alignment: 'site-nav--centered' %}
  </nav>
  {% endif %}
</div>



<script type="application/ld+json">
{
  "@context": "http://schema.org",
  "@type": "Organization",
  "name": {{ shop.name | json }},
  {% if section.settings.logo %}
    {% assign image_size = section.settings.logo.width | append:'x' %}
    "logo": {{ section.settings.logo | img_url: image_size | prepend: "https:" | json }},
  {% endif %}
  "sameAs": [
    {{ settings.social_twitter_link | json }},
    {{ settings.social_facebook_link | json }},
    {{ settings.social_pinterest_link | json }},
    {{ settings.social_instagram_link | json }},
    {{ settings.social_tumblr_link | json }},
    {{ settings.social_snapchat_link | json }},
    {{ settings.social_youtube_link | json }},
    {{ settings.social_vimeo_link | json }}
  ],
  "url": {{ shop.url | append: page.url | json }}
}
</script>

{% if template.name == 'index' %}
  {% assign potential_action_target = shop.url | append: "/search?q={search_term_string}" %}
  <script type="application/ld+json">
    {
      "@context": "http://schema.org",
      "@type": "WebSite",
      "name": {{ shop.name | json }},
      "potentialAction": {
        "@type": "SearchAction",
        "target": {{ potential_action_target | json }},
        "query-input": "required name=search_term_string"
      },
      "url": {{ shop.url | append: page.url | json }}
    }
  </script>
{% endif %}

{% schema %}
{
  "name": {
    "de": "Titel",
    "en": "Header",
    "es": "Encabezado",
    "fr": "En-tête",
    "it": "Header",
    "ja": "ヘッダー",
    "nl": "Koptekst",
    "pt-BR": "Cabeçalho",
    "zh-CN": "标头",
    "zh-TW": "標頭"
  },
  "settings": [
    {
      "type": "radio",
      "id": "align_logo",
      "label": {
        "de": "Logo-Ausrichtung",
        "en": "Logo alignment",
        "es": "Alineación de logotipo",
        "fr": "Alignement du logo",
        "it": "Allineamento logo",
        "ja": "ロゴアラインメント",
        "nl": "Logo-uitlijning",
        "pt-BR": "Alinhamento do logotipo",
        "zh-CN": "logo 对齐方式",
        "zh-TW": "商標對齊"
      },
      "default": "left",
      "options": [
        {
          "value": "left",
          "label": {
            "de": "Links",
            "en": "Left",
            "es": "Izquierda",
            "fr": "Gauche",
            "it": "Sinistra",
            "ja": "左",
            "nl": "Links",
            "pt-BR": "Esquerda",
            "zh-CN": "左侧",
            "zh-TW": "左方"
          }
        },
        {
          "value": "center",
          "label": {
            "de": "Mittig",
            "en": "Centered",
            "es": "Centrada",
            "fr": "Centré",
            "it": "Centrato",
            "ja": "中央揃え",
            "nl": "Gecentreerd",
            "pt-BR": "Centralizado",
            "zh-CN": "居中",
            "zh-TW": "置中"
          }
        }
      ]
    },
    {
      "type": "image_picker",
      "id": "logo",
      "label": {
        "de": "Logo-Foto",
        "en": "Logo image",
        "es": "Logotipo",
        "fr": "Image du logo",
        "it": "Immagine del logo",
        "ja": "ロゴ画像",
        "nl": "Afbeelding van logo",
        "pt-BR": "Imagem do logotipo",
        "zh-CN": "logo 图片",
        "zh-TW": "標誌圖片"
      }
    },
    {
      "type": "range",
      "id": "logo_max_width",
      "label": {
        "de": "Logobreite",
        "en": "Custom logo width",
        "es": "Ancho del logotipo personalizado",
        "fr": "Largeur personnalisée du logo",
        "it": "Larghezza logo personalizzato",
        "ja": "ロゴの幅をカスタムする",
        "nl": "Aangepaste logo-breedte",
        "pt-BR": "Largura do logotipo personalizado",
        "zh-CN": "自定义 logo 宽度",
        "zh-TW": "自訂商標寬度"
      },
      "min": 50,
      "max": 250,
      "step": 5,
      "unit": {
        "de": "px",
        "en": "px",
        "es": "px",
        "fr": "px",
        "it": "px",
        "ja": "px",
        "nl": "px",
        "pt-BR": "px",
        "zh-CN": "px",
        "zh-TW": "像素"
      },
      "default": 100
    },
    {
      "type": "link_list",
      "id": "main_linklist",
      "label": {
        "de": "Menü",
        "en": "Menu",
        "es": "Menú",
        "fr": "Menu",
        "it": "Menu",
        "ja": "メニュー",
        "nl": "Menu",
        "pt-BR": "Menu",
        "zh-CN": "菜单",
        "zh-TW": "選單"
      },
      "default": "main-menu"
    },
    {
      "type": "header",
      "content": {
        "de": "Ankündigungsbereich",
        "en": "Announcement bar",
        "es": "Barra de anuncios",
        "fr": "Barre d'annonces",
        "it": "Barra degli annunci",
        "ja": "告知バー",
        "nl": "Aankondigingsbalk",
        "pt-BR": "Barra de anúncios",
        "zh-CN": "公告栏",
        "zh-TW": "公告列"
      }
    },
    {
      "type": "checkbox",
      "id": "message",
      "label": {
        "de": "Ankündigung anzeigen",
        "en": "Show announcement",
        "es": "Mostrar anuncio",
        "fr": "Afficher l'annonce",
        "it": "Mostra annuncio",
        "ja": "告知を表示する",
        "nl": "Aankondiging weergeven",
        "pt-BR": "Exibir anúncio",
        "zh-CN": "显示公告",
        "zh-TW": "顯示公告"
      },
      "default": false
    },
    {
      "type": "checkbox",
      "id": "home_page_only",
      "label": {
        "de": "Nur Startseite",
        "en": "Home page only",
        "es": "Solo página de inicio",
        "fr": "Page d'accueil uniquement",
        "it": "Solo home page",
        "ja": "ホームページのみ",
        "nl": "Alleen startpagina",
        "pt-BR": "Apenas página inicial",
        "zh-CN": "仅主页",
        "zh-TW": "僅限首頁"
      },
      "default": true
    },
    {
      "type": "text",
      "id": "message_text",
      "label": {
        "de": "Text",
        "en": "Text",
        "es": "Texto",
        "fr": "Texte",
        "it": "Testo",
        "ja": "テキスト",
        "nl": "Tekst",
        "pt-BR": "Texto",
        "zh-CN": "文本",
        "zh-TW": "文字"
      },
      "default": {
        "de": "Hier etwas ankündigen",
        "en": "Announce something here",
        "es": "Anuncia algo aquí",
        "fr": "Annoncez quelque chose ici",
        "it": "Annuncia qualcosa qui",
        "ja": "ここで告知してください",
        "nl": "Kondig hier iets aan",
        "pt-BR": "Anuncie algo aqui",
        "zh-CN": "在此处进行公告",
        "zh-TW": "在此公告資訊"
      }
    },
    {
      "type": "url",
      "id": "message_link",
      "label": {
        "de": "Link",
        "en": "Link",
        "es": "Enlace",
        "fr": "Lien",
        "it": "Link",
        "ja": "リンク",
        "nl": "Link",
        "pt-BR": "Link",
        "zh-CN": "链接",
        "zh-TW": "連結"
      },
      "info": {
        "de": "Optional",
        "en": "Optional",
        "es": "Opcional",
        "fr": "Facultatif",
        "it": "Facoltativo",
        "ja": "選択させる",
        "nl": "Optioneel",
        "pt-BR": "Opcional",
        "zh-CN": "可选",
        "zh-TW": "(選填)"
      }
    },
    {
      "type": "color",
      "id": "color_bg",
      "label": {
        "de": "Zeile",
        "en": "Bar",
        "es": "Barra",
        "fr": "Barre",
        "it": "Barra",
        "ja": "バー",
        "nl": "Balk",
        "pt-BR": "Barra",
        "zh-CN": "栏",
        "zh-TW": "橫條"
      },
      "default": "#7796a8"
    },
    {
      "type": "color",
      "id": "color_text",
      "label": {
        "de": "Text",
        "en": "Text",
        "es": "Texto",
        "fr": "Texte",
        "it": "Testo",
        "ja": "テキスト",
        "nl": "Tekst",
        "pt-BR": "Texto",
        "zh-CN": "文本",
        "zh-TW": "文字"
      },
      "default": "#fff"
    }
  ]
}
{% endschema %}
</pre>
