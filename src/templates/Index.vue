<template>
  <component :is="type" class="index">
    <NavBar
      active="Index"
      title="Internet of Humans"
      :navItems="[
        { name: 'Participate', href: '#participate' },
        { name: 'Events', href: '#events' },
        { name: 'Organisers', href: '#organisers' },
        { name: 'Topics', href: '#topics' },
        { name: 'About Edgeryders', href: '#edgeryders' },
      ]"
    />
    <Hero />

    <Wrapper class="standard" id="participate">
      <SectionTitle glyph="participate">How to Participate</SectionTitle>
      <Paragraph>
        The Internet of Humans Project is a quest to understand how we the people, who contribute
        towards building the evolution of the Internet and our digital technologies, actively build
        a better future against a backdrop of massive social, economic, ecological and political
        challenges.
      </Paragraph>
      <ul>
        <li>
          <a href="http://communities.edgeryders.eu" target="_blank">Sign up here</a> to join our
          community at Edgeryders.
        </li>
        <li>
          Join a
          <a href="https://zoom.us/j/3063210325" target="_blank">Virtual Cafe community call</a> –
          an online video gathering to meet other community members! Calls are hosted in English.
        </li>
        <li>
          <a href="http://edgeryders.eu/c/ioh" target="_blank">Tell your story</a>. What is a
          burning question that drives your work? How did you get started and what hurdles have you
          met along the way? Which doubts do you have about the work you are doing and the path
          forward? What kind of support would you like to offer others traveling a similar path?
        </li>
      </ul>
    </Wrapper>

    <Wrapper class="standard" id="news">
      <SectionTitle glyph="news">Latest Topics</SectionTitle>

      <carousel :perPageCustom="[[10, 1], [890, 3]]" :paginationEnabled="true">
        <slide v-for="post in latestPosts" :key="post.created_at">
          <div class="post" :style="{ background: 'url(' + post.image_url + ')' }">
            <p class="date">{{ post.created_at | formatDate }}</p>
            <a :href="'https://edgeryders.eu/t/' + post.slug" target="_blank" class="title">{{
              post.title
            }}</a>
            <div class="excerpt">
              {{
                post.excerpt
                  .replace(/(<([^>]+)>)/gi, "")
                  .replace(/\s*\[.*?\]\s*/g, "")
                  .substring(0, 215)
              }}...
            </div>
          </div>
        </slide>
      </carousel>
    </Wrapper>

    <Wrapper class="standard" id="events" v-if="allTopicsByTag">
      <SectionTitle glyph="events">Latest Events</SectionTitle>

      <div class="table">
        <ul class="key" :class="{ active: activeTab }">
          <li
            v-for="(event, index) in getEvents(allTopicsByTag.children)"
            @click="
              activeKey = event
              activeTab = true
            "
            :class="{ active: activeKey.title == event.title }"
          >
            {{ event.title }}
          </li>
        </ul>

        <div class="field" :class="{ active: activeTab }">
          <div v-if="activeKey">
            <div class="back mobile" @click="activeTab = false">< Back to Events</div>

            <h3 v-html="activeKey.title"></h3>

            <MetaData
              :image="
                'https://edgeryders.eu' +
                  getMember('id', activeKey.posters[0].user_id).avatar_template.replace(
                    '{size}',
                    '200'
                  )
              "
              variation="profile"
              stroke="white"
            >
              @{{ getMember("id", activeKey.posters[0].user_id).username }}
            </MetaData>

            <MetaData variation="tag" stroke="white"> {{ activeKey.views }} Views </MetaData>

            <div class="event_excerpt" style="clear: both !important">
              {{ activeKey.postexcerpt.replace(/(<([^>]+)>)/gi, "").replace(/\s*\[.*?\]\s*/g, "") }}
            </div>

            <MetaData
              v-for="tag in activeKey.tags"
              variation="tag"
              fill="rgba(0,0,0,0.15)"
              stroke="rgba(0,0,0,0.1)"
              :key="tag"
            >
              # {{ tag }}
            </MetaData>
          </div>
        </div>
      </div>
    </Wrapper>

    <Wrapper class="standard" id="organisers">
      <SectionTitle glyph="organisers">Organisers</SectionTitle>

      <carousel :perPageCustom="[[10, 1], [620, 3]]" :paginationEnabled="true" v-if="bios">
        <slide v-for="organiser in organisers" :key="organiser.username">
          <div class="profile">
            <a :href="'https://edgeryders.eu/u/' + organiser.username" target="_blank">
              <div class="user">
                <img
                  v-if="getMember('username', organiser.username)"
                  :src="
                    'https://edgeryders.eu/' +
                      getMember('username', organiser.username).avatar_template.replace(
                        '{size}',
                        '200'
                      )
                  "
                />

                <img
                  v-else
                  :src="
                    'https://edgeryders.eu/' +
                      fetchMember(organiser.username).avatar_template.replace('{size}', '200')
                  "
                />

                <div class="meta">
                  <h3>@{{ organiser.username }}</h3>
                  <p class="byline">{{ organiser.byline }}</p>
                </div>
              </div>
            </a>

            <p class="about">{{ organiser.bio.substring(0, 240) }}...</p>
            <Button
              type="a"
              :href="'https://edgeryders.eu/u/' + organiser.username + '/summary'"
              fill="blue"
              variation="edgeryders"
              >Connect with on Edgeryders</Button
            >
          </div>
        </slide>
      </carousel>
    </Wrapper>

    <Wrapper class="standard desktop" id="topics" v-if="allTopicsByTag">
      <sunburst
        class="sunburst"
        :data="allTopicsByTag"
        :minAngleDisplayed="minAngleDisplayed"
        :colorScheme="colorScheme"
        :inAnimationDuration="inAnimationDuration"
        :outAnimationDuration="outAnimationDuration"
      >
        <nodeInfoDisplayer
          slot="top"
          slot-scope="{ nodes }"
          :current="nodes.clicked"
          :root="nodes.root"
          :clicked="nodes.clicked"
          :posts="topics"
          :users="users"
        />

        <template slot-scope="{ nodes, actions }">
          <highlightOnHover :nodes="nodes" :actions="actions" />
          <zoomOnClick :nodes="nodes" :actions="actions" />

          <tagDisplayer
            :current="nodes.mouseOver"
            :root="nodes.root"
            :clicked="nodes.clicked"
            slot="legend"
          />
        </template>
      </sunburst>
    </Wrapper>

    <Wrapper class="footer" id="edgeryders">
      <SectionTitle glyph="edgeryders">About Edgeryders</SectionTitle>
      <Paragraph
        >If you are new to Edgeryders: we are a global network of 5000 people who are interested in
        participation, health, technology, social progress, innovation, and many more topics. We
        come from all walks of life to access interesting information, make new connections and
        collaborate. By sharing experiences with one another we turn our collective knowledge into
        useful advice to make better decisions for ourselves and our families in the near
        future.</Paragraph
      >

      <div class="links">
        <Button type="a" href="https://edgeryders.eu/c/ioh" fill="white" variation="arrow"
          >Start a Conversation</Button
        >
        <Button type="a" href="https://er-campaigns.surge.sh" fill="white" variation="arrow"
          >View Our Campaigns</Button
        >
        <Social fill="white" network="twitter" />
        <Social fill="white" network="facebook" />
      </div>
    </Wrapper>
  </component>
</template>

<script>
import { Asyncable } from "vue-asyncable"
import axios from "axios"
import moment from "moment"
import Hero from "@/components/hero"
import { Carousel, Slide } from "vue-carousel"
import sunburst from "@/components/sunburst"
import nodeInfoDisplayer from "@/components/nodeInfoDisplayer"
import tagDisplayer from "@/components/tagDisplayer"
import breadcrumbTrail from "@/components/breadcrumbTrail"
//behaviours
import highlightOnHover from "@/components/highlightOnHover"
import zoomOnClick from "@/components/zoomOnClick"
import { colorSchemes } from "@/infra/colorSchemes"
const colorSchemesNames = Object.keys(colorSchemes).map(key => ({
  value: key,
  text: colorSchemes[key].name,
}))

export default {
  name: "Index",
  status: "ready",
  release: "1.0.0",
  metaInfo: {
    title: "Internet of Humans | Edgeryders",
    htmlAttrs: {
      lang: "en",
    },
  },
  data() {
    return {
      allTopicsByTag: null,
      minAngleDisplayed: 0.05,
      colorScheme: colorSchemesNames[0].value,
      colorSchemes: colorSchemesNames,
      inAnimationDuration: 100,
      outAnimationDuration: 1000,
      latestPosts: null,
      topics: [],
      sorted: [],
      activeKey: "",
      activeTab: false,
      posts: null,
      jsonArr: [],
      bios: [],
      organisers: [
        {
          name: "John Coate",
          username: "johncoate",
          byline: "Co-Director and Community Manager, Edgeryders",
          bio:
            "John was employee number two at The WELL, where he was instrumental in creating the online community that Wired Magazine called the “world’s most influential”. There he was the first to work as what is now known as an “online community manager” and he wrote the first treatise on building online community. He co-founded the first major news website, sfgate.com, which today has more than thirty million monthly visitors and more than 340K Twitter followers. He was the online manager of a teen social network and game site that had thousands of members. He managed a regional media organisation that combined terrestrial radio and the internet in innovative ways. Through it all the core of his community knowledge comes from direct personal experience living and working with others who are consciously building lasting relationships as the building blocks of community.",
        },
        {
          name: "Nadia E.N.",
          username: "nadia",
          byline: "Co-Founder, Edgeryders",
          bio:
            "Engineer and designer born in Sweden to African parents, raised in Europe and Asia. A bridge person between activist networks of marginalised groups, artists, civil society, entrepreneurs, media and government. Nadia’s role in Edgeryders to serve our global community of brilliant misfits via the not-for-profit company that builds and managers the infrastructure sustaining it. In practice this translates into laying out the vision, building strategic partnerships, driving flagship projects and managing crews on different projects. Named Minister of Labour in a “dream government of New Thinkers” imagined for Sweden by the leading financial newspaper in Scandinavia.",
        },
        {
          name: "Alberto Cottica",
          username: "alberto",
          byline: "Co-Founder and Research Director, Edgeryders",
          bio:
            "Data/Network Scientist and Economist. An expert on collaborative governance and participation, with proven track record of managing processes of ICT-enabled design and delivery of public policy – and even public services – in collaboration with citizens. Alberto has first-hand experience in establishing, nurturing and running communities of citizens that work towards common goals, sometimes in alliance with government). Also has a proven track record of driving adoption of innovative practices – and, more importantly, of a practice of openness and transparency in policy delivery – in fairly conservative large organisations, including government agencies.",
        },
        {
          name: "Hugi Ásgeirsson",
          username: "hugi",
          byline: "Co-Director, Edgeryders",
          bio:
            "Positioned between technology and participatory culture and politics, Hugi is interested in how people can collaborate better together today and in the future, online and offline. He currently works from Stockholm, where he co-founded the participatory culture center and social enterprise Blivande. As a co-director of Edgeryders, he runs the development lab Participio developing software for participatory culture and is involved in a number of projects exploring how technology can enable participation, social cohesion and resilience. Hugi has a background in informatics and analytics and has a degree from KTH Royal Institute of Technology where he studied biotechnology engineering.",
        },
        {
          name: "Maria Euler",
          username: "MariaEuler",
          byline: "Community Manager, Edgeryders",
          bio:
            "Interdisciplinary artist and design researcher as well as a community manager for edgeryder with a background in physics, fine art and a Master in Information Experience Design from the Royal Collge of Art. She searches for points of connection and entry, between disciplines, complex scientific concepts, discussion, emotional and tangible experiences and people. She worked with the Helen Hamlyn Centre for Inclusive Design and the Financial Education startup Gimi as a researcher and UX Designer. She also was part of multiple successful art-science collaborations as for example with the Bristol Laboratory for Quantum Computing and exhibited interactive installations at venues and festivals across Europe such as Sonar+D, I-Gem and VrSci. Her goal is to enable different approaches and connections to increase the diversity and creativity of the discourse on art, science and technology.",
        },
        {
          name: "Amelia Hassoun",
          username: "amelia",
          byline: "Lead Ethnographer, Edgeryders",
          bio:
            "An anthropologist with a research focus on the social impacts of digital technologies. Has extensive ethnographic research experience both online and offline, in both public and private sector, focusing on the power of participatory communities to facilitate transparent, efficient solutions to public problems. Has technical experience designing and developing a bespoke patient information website, as well as interviewing community members, gathering and analysing qualitative and quantitative data, and generating ethnographic reports. Currently a doctoral researcher at the Oxford Internet Institute analysing community-based civic technology initiatives, ethnographically examining how community members creatively interact with and rework “smart” urban technologies.",
        },
      ],
      jsonObj: null,
      users: null,
    }
  },
  filters: {
    formatDate: function(value) {
      return moment(String(value)).format("MM/DD/YYYY")
    },
  },
  async mounted() {
    var i = 0
    var x = 1

    this.fetchUser(["amelia"])

    let allMembers = await axios.get(
      "https://api.particip.io/get-data?endpoint=https://edgeryders.eu/c/ioh"
    )

    this.users = allMembers.data.users

    let posts = await axios.get(
      "https://api.particip.io/get-data?endpoint=https://edgeryders.eu/c/ioh"
    )

    let allTopics = posts.data.topic_list.topics

    this.latestPosts = allTopics
      .sort((a, b) => (a.last_posted_at < b.last_posted_at ? 1 : -1))
      .slice(0, 12)

    while (i < x) {
      let response = await axios.get(
        "https://api.particip.io/get-data?endpoint=https://edgeryders.eu/c/ioh?page=" + i
      )
      if (!response.data.users) {
        x = 10
        i = 10
        var merged = [].concat.apply([], this.topics)

        this.topics = merged

        var topicsByTag = merged.reduce(
          (acc, { id, tags, title, posts_count, like_count, slug, views, posters, excerpt }) => {
            tags.forEach(t => {
              acc[t] = acc[t] || []
              const size = 1
              const tag = t
              const postexcerpt = excerpt.replace(/&quot;/g, '\\"')

              acc[t].push({
                id,
                title,
                posts_count,
                like_count,
                views,
                tag,
                slug,
                tags,
                posters,
                postexcerpt,
                size,
              })
            })
            return acc
          },
          {}
        )

        Object.entries(topicsByTag).forEach(([key, value]) => {
          const obj = {
            title: key,
            children: value,
          }

          this.jsonArr.push(obj)
        })

        var jsonObj = function(topics) {
          ;(this.title = "Internet of Humans"), (this.children = topics)
        }

        var taggedTopics = new jsonObj(this.jsonArr)

        this.allTopicsByTag = taggedTopics
      } else {
        x++
        i++
        this.topics.push(response.data.topic_list.topics)
      }
    }
  },
  components: {
    sunburst,
    nodeInfoDisplayer,
    tagDisplayer,
    Hero,
    breadcrumbTrail,
    highlightOnHover,
    Carousel,
    Slide,
    zoomOnClick,
  },
  props: {
    type: {
      type: String,
      default: "div",
    },
  },
  methods: {
    getEvents(value) {
      var result = value.filter(obj => {
        return obj["title"] == "event"
      })
      return result[0].children
    },
    getMember(key, value) {
      var user = this.users.filter(obj => {
        return obj[key] == value
      })
      return user[0]
    },
    fetchMember(value) {
      var user = this.bios.filter(obj => {
        return obj["username"] == value
      })
      return user[0]
    },
    fetchUser(usernames) {
      for (var i = 0; i < usernames.length; i++) {
        axios
          .get("https://api.particip.io/get-data?endpoint=https://edgeryders.eu/u/" + usernames[i])
          .then(
            response => {
              this.bios.push(response.data.user)
            },
            error => {}
          )
      }
    },
  },
}
</script>

<style lang="scss" scoped>
.wrapper.standard {
  width: 100%;
  display: inline-block;
  padding: 0 64px 64px;
}
.wrapper.footer {
  width: 100%;
  display: inline-block;
  padding: 64px;
}
.desktop {
  display: block;
}
.mobile {
  display: none;
}
div /deep/ .VueCarousel-dot-container .VueCarousel-dot--active {
  background-color: blue !important;
}
#participate {
  padding-top: 64px;
  padding-bottom: 0;
  .paragraph {
    color: black !important;
    padding: 25px 40px;
    background: #efefef;
    width: 45%;
    float: left;
    font-size: 1.2em;
  }
  ul {
    padding: 0 !important;
    width: 45%;
    float: left;
    padding: 0;
    margin: 0 0 0 5%;
    li {
      list-style: none;
      display: block;
      position: relative;
      margin-bottom: 10px;
      counter-increment: inst;
      padding: 8px 13px 8px 55px;
      &:before {
        content: counter(inst);
        width: 34px;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 34px;
        background: blue;
        color: white;
        position: absolute;
        left: 4px;
        top: 5px;
      }
    }
  }
}
#news {
  .post {
    width: 95%;
    height: 350px;
    overflow: hidden;
    margin: 0 2.5% 3% 0;
    display: inline-block;
    float: left;
    background: #000;
    position: relative;
    background-color: #000 !important;
    &:before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      opacity: 0.6;
      z-index: 9;
      background: #000;
    }
    .date {
      color: white;
      border: 2px solid white;
      border-radius: 10px;
      position: relative;
      width: auto;
      display: inline-block;
      padding: 6px 14px 4px;
      z-index: 99999;
      top: 10px;
      left: 20px;
    }
    .excerpt {
      color: white !important;
      overflow: hidden;
      position: absolute;
      bottom: 0;
      background: blue;
      font-size: 14px;
      padding: 4% 5% !important;
      z-index: 99999;
      width: 90%;
      transition: transform 0.65s ease;
    }
    a.title {
      padding: 0 20px 0;
      top: 10px;
      opacity: 1;
      z-index: 99999;
      text-decoration: none;
      font-weight: bold;
      color: white;
      display: block;
      line-height: 33px;
      text-shadow: 0px 1px 0px rgba(0, 0, 0, 0.7);
      position: relative;
      font-size: 22px;
    }
    .thumbnail {
      position: absolute;
      top: 0;
      width: 100%;
      height: 100%;
      left: 0;
      z-index: 2;
      opacity: 0.2;
      background-size: cover !important;
      background-repeat: no-repeat !important;
    }
  }
}
#events {
  background: blue;
  color: white;
  padding-top: 30px;
  /deep/ .section_title {
    margin-left: 10px;
  }
  .table {
    width: 100%;
    display: flex;
    margin: 0;
    .key {
      flex-basis: 50%;
      list-style: none;
      padding: 0;
      height: 420px;
      overflow: scroll;
      li {
        width: 90%;
        padding: 14px;
        border-bottom: 1px solid rgba(255, 255, 255, 0.2);
        &.active {
          background: #1f7ad6;
          color: #fff;
          border-color: #1f7ad6;
          border-top: 1px solid #1f7ad6;
          margin-top: -1px;
        }
        &:hover {
          cursor: pointer;
          background: rgba(0, 0, 0, 0.05);
        }
      }
    }
    .field {
      flex-basis: 50%;
      padding: 20px;
      .field_header {
        margin: 0 0 10px 0;
        display: inline-block;
        width: 100%;
        border-bottom: 1px solid rgba(0, 0, 0, 0.1);
      }
      h2 {
        border-bottom: 1px solid rgba(0, 0, 0, 0.1);
        padding: 0 0 15px 0;
        display: block;
        width: 100%;
        margin: 0 0 20px 0 !important;
      }
      p {
        margin: 0 0 20px 0;
      }
      .event_excerpt {
        margin: 20px 0 !important;
      }
      .user {
        padding: 0 12px 0 0;
        border-radius: 10px;
        font-size: 14px;
        border-color: #1f7ad6 !important;
        overflow: hidden;
        height: 40px;
        align-items: center;
        background: rgba(0, 0, 0, 0.2);
        color: #fff !important;
        margin: 0 15px 0px 0 !important;
        font-weight: bold;
        border: 2px solid #258aef;
        display: inline-flex;
        margin: 0px 0;
        float: left;
        img {
          width: auto;
          height: 92%;
          border-radius: 7px;
          margin: 0 10px 0 2px;
        }
      }
      .byline {
        font-weight: bold;
      }
    }
  }
  h2 {
    margin: 15px 15px;
  }
  p {
    margin: 20px 15px;
  }
}
#organisers {
  padding-top: 30px;
  .profile {
    width: 80%;
    background: #efefef;
    padding: 5%;
    border-radius: 10px;
    a {
      text-decoration: none !important;
      &.profile_link {
        background: blue;
        color: white;
        padding: 8px 12px;
        border-radius: 10px;
      }
    }
    .user {
      display: flex;
      justify-content: space-between;
      img {
        width: 90px;
        height: 90px;
        border-radius: 20px;
        float: left;
      }
      .meta {
        height: 90px;
        padding: 0 15px 0;
        display: flex;
        flex-direction: column;
        justify-content: center;
        width: 100%;
        color: blue !important;
        h3 {
          margin: 0;
          text-decoration: none;
        }
        p {
          margin: 0;
          font-size: 0.9em;
        }
      }
    }
  }
}
#topics {
  padding-bottom: 0;
  .postInfo {
    padding: 15px 20px;
    margin-left: -20px;
    width: 100%;
    height: auto;
    background: rgba(0, 0, 0, 0.02);
  }
  .tag_browser {
    width: 100%;
    position: relative;
    h1 {
      display: block;
      width: 100%;
    }
  }
  div /deep/ .sunburst {
    width: 100% !important;
    height: 500px;
  }
  .sunburst {
    width: 90vw !important;
  }
}
#edgeryders {
  background: rgba(0, 0, 0, 1);
  margin: 30px 0 0 0;
  position: relative;
  /deep/ .section_title {
    margin-bottom: 0;
  }
  h1 {
    float: none;
    border: 2px solid white;
    display: inline-flex;
    align-items: center;
    margin: 0;
    font-size: 23px;
    padding: 2px 20px 2px 3px !important;
    border-radius: 10px;
    color: white !important;
    .logo {
      margin: 10px 14px 0 10px;
      width: 38px !important;
    }
  }
  .paragraph {
    color: white !important;
    width: 60%;
    float: left;
    font-size: 18px;
    padding-right: 20px;
    border-right: 2px solid #ddd;
  }
  p {
    margin: 20px 0;
    padding: 0;
  }
  a {
    text-decoration: none;
    display: block;
    margin-bottom: 20px;
  }
  .links {
    float: left;
    margin: 30px;
    width: 30%;
  }
}
@media only screen and (max-width: 860px) {
  .desktop {
    display: none !important;
  }
  .mobile {
    display: block;
  }
  .standard {
    padding: 10px 30px 0px !important;
  }
  .footer {
    padding: 10px 40px 0px !important;
  }
  #news {
    .VueCarousel /deep/ .VueCarousel-dot-container {
      margin: 0 !important;
    }
    .post {
      width: 100%;
      margin: 0;
      .excerpt {
        font-size: 3vw;
      }
    }
  }
  #participate {
    .paragraph {
      width: 100%;
    }
    ul {
      width: 100%;
      padding: 0;
      margin: 0;
      li {
      }
    }
  }
  .links a {
    float: none !important;
    display: block !important;
  }
}
@media only screen and (max-width: 600px) {
  #organisers {
    padding-bottom: 30px !important;
    .profile {
      width: 85%;
      margin: 0 3%;
      border-radius: 10px;
      display: block;
      overflow: hidden;
      border: 1px solid #efefef;
      background: none;
      .user {
        background: #efefef;
        padding: 0px 0 10px;
        border-radius: 10px;
        img {
          width: 80px;
          height: 80px;
          margin: 3% 3% 0 3%;
          border-radius: 10px;
          float: left;
          margin-right: 0px;
          background: #fff;
        }
      }
      a {
        text-decoration: none !important;
      }
      .meta {
        p {
          margin: 0;
          &:first-child {
            font-size: 1.4em;
            padding: 10px 0 0 0;
            color: black;
            font-weight: bold;
          }
        }
      }
      .about {
        padding: 0 0px;
      }
    }
    .bio {
      clear: both !important;
      display: block;
      width: 100%;
      border: none;
      .profile {
        width: 100%;
      }
    }
  }
  #events {
    overflow: hidden;
    margin: 30px 0 0 0;
    padding-bottom: 20px !important;
    .table {
      width: 250%;
      justify-content: space-between;
      margin: 0 0 20px 0;
      display: inline-flex;
      height: 420px;
      .back {
        background: blue;
        color: white;
        border: 2px solid white;
        padding: 10px 12px;
        margin: 0 0 20px 0;
        width: 90%;
      }
      .key {
        flex-basis: 105%;
        height: 100%;
        margin: 0 20% 0 0;
        transition: all 0.45s ease;
        &.active {
          transform: translateX(-104%) !important;
        }
        li {
          padding: 14px 10px;
        }
      }
      .field {
        flex-basis: 100%;
        margin-left: 0%;
        height: 100%;
        overflow: scroll;
        padding: 0 !important;
        transition: transform 0.45s ease;
        &.active {
          transform: translateX(-155%) !important;
          background: blue;
          h1 {
            width: 100%;
            float: none;
          }
        }
      }
    }
  }
  #edgeryders {
    padding-bottom: 0px !important;
    width: 100% !important;
    .section_title {
      margin-bottom: 20px;
    }
    h1 {
      width: 90%;
      margin: 0px 0 20px !important;
    }
    .paragraph {
      width: 100%;
      border: none !important;
      padding: 5px !important;
      margin-top: 0 !important;
    }
    .links {
      width: 100%;
      padding: 0 0 0px 5px;
      margin: 0;
    }
    a {
      margin: 0 0px 20px 0;
      font-size: 15px;
      display: inline-block;
      text-decoration: none !important;
      /deep/ div {
        text-decoration: none !important;
      }
      &.button {
        padding-right: 45px;
        margin: 0 10px 20px 0;
      }
    }
  }
}
</style>

<docs>
  ```jsx
  <Index />
  ```
</docs>
