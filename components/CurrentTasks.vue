
<script>
  import { Octokit } from "octokit";
  import { ref } from 'vue';

  export default {
    name: 'CurrentTasks',
    setup() {
      const token = import.meta.env.VITE_APP_GH_CRED;
      const octokit = new Octokit({
        auth: token
      });

      const swimLanes = ref([]);
      const tasks = ref(["task1", "task2", "task3", "task4", "task5"]);
      const projects = ref([]);
      const projectFields = ref([]);
      const projectCards = ref({});
      const isAuthenticated = ref(false);
      let laneIndex = ref(0);

       async function testAuthentication() {
        try {
          const response = await octokit.request('GET /user');
          isAuthenticated.value = true;
        } catch (error) {
          isAuthenticated.value = false;
          console.error('Authentication failed:', error);
        }
      }

      function updateLaneIndex() {
        if (this.laneIndex < this.swimLanes.length - 1) {
          this.laneIndex++;
        } else {
          this.laneIndex = 0;
        }
      }

      async function fetchProjects(owner, repo) {
        const query = `
          query($owner: String!, $repo: String!) {
            repository(owner: $owner, name: $repo) {
              projectsV2(first: 10) {
                nodes {
                  id
                  title
                  items(first: 10) {
                    nodes {
                      id
                      content {
                        ... on Issue {
                          title
                          body
                        }
                        ... on PullRequest {
                          title
                          body
                        }
                      }
                    }
                  }
                }
              }
            }
          }
        `;

      try {
        const response = await octokit.graphql(query, {
          owner: owner,
          repo: repo
        });
        projects.value = response.repository.projectsV2.nodes;
      } catch (error) {
        console.error('Error fetching projects from GitHub GraphQL API:', error);
      }
    }

    async function fetchProjectFieldsAndItems(projectId) {
      const query = `
        query($projectId: ID!) {
          node(id: $projectId) {
            ... on ProjectV2 {
              fields(first: 10) {
                nodes {
                  __typename
                  ... on ProjectV2FieldCommon {
                    id
                    name
                  }
                  ... on ProjectV2SingleSelectField {
                    id
                    name
                    options {
                      id
                      name
                    }
                  }
                  ... on ProjectV2IterationField {
                    id
                    name
                    configuration {
                      iterations {
                        startDate
                        duration
                        title
                      }
                    }
                  }
                }
              }
              items(first: 100) {
                nodes {
                  id
                  fieldValues(first: 10) {
                    nodes {
                      ... on ProjectV2ItemFieldSingleSelectValue {
                        field {
                          ... on ProjectV2SingleSelectField {
                            id
                          }
                        }
                        optionId
                      }
                    }
                  }
                  content {
                    ... on Issue {
                      id
                      title
                    }
                    ... on PullRequest {
                      id
                      title
                    }
                  }
                }
              }
            }
          }
        }
      `;

      try {
        const response = await octokit.graphql(query, {
          projectId: projectId
        });
        const fields = response.node.fields.nodes;
        const items = response.node.items.nodes;

        // Check if the third field is of type ProjectV2SingleSelectField
        if (fields[2].__typename === 'ProjectV2SingleSelectField') {
          const options = fields[2].options;
          options.forEach(option => {
            swimLanes.value.push(option.name);
            const optionItems = items.filter(item =>
              item.fieldValues.nodes.some(fieldValue =>
                fieldValue.__typename === 'ProjectV2ItemFieldSingleSelectValue' &&
                fieldValue.field.id === fields[2].id &&
                fieldValue.optionId === option.id
              )
            );
          });
        } else {
          console.log('fields.nodes[2] is not of type ProjectV2SingleSelectField');
        }
      } catch (error) {
        console.error('Error fetching project fields and items from GitHub GraphQL API:', error);
        if (error.errors) {
          error.errors.forEach(e => console.error(e.message)); // Log specific error messages
        }
      }
    }

      // Fetch data when the component is mounted
      async function fetchData() {
      await testAuthentication();
      if (isAuthenticated.value) {
        const owner = import.meta.env.VITE_APP_GH_OWNER;
        const repo = import.meta.env.VITE_APP_GH_REPO;
        await fetchProjects(owner, repo);
        if (projects.value.length > 0) {
          await fetchProjectFieldsAndItems(projects.value[0].id);
        }
      }
    }
    fetchData();

    return {
      swimLanes,
      tasks,
      projectFields,
      projectCards,
      projects,
      isAuthenticated,
      laneIndex,
      updateLaneIndex
    };
  }
};
</script>

<template>
  <div class="swimlanes-container">
    <div class="swimlane">
      {{ swimLanes[laneIndex] }}
    </div>
    <div class="svg-arrow" @click="updateLaneIndex" >
      <svg width="3em" height="3em" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M4 12H20M20 12L16 8M20 12L16 16" stroke="#FFF22F" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
      </svg>
    </div>
    <div class="tasks-container">
      <div v-for="task in tasks" :key="task" class="tasks">
        <div class="task-item">{{ task }}</div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .swimlanes-container {
    overflow-y: hidden;
  }

  .swimlane {
    color:#FFF22F;
    font-family: 'NOIRetBLANC', serif;
    font-size: 2em;
    min-height: 2em;
  }

  .svg {
    width: max-content;
  }

  .svg-arrow {
    cursor: pointer;
    position: absolute;
    right: 3em;
    transform: translateY(-3em);
  }

  .tasks-container {
    overflow-y: scroll;
    padding-bottom: 4em;
    height: 50vh;
  }

  .tasks {
    height: max-content;
  }

  .task-item {
    color: white;
    border: 1px solid #dbdbdb81;
    height: 100px;
    margin: 2em 0;
  }
</style>