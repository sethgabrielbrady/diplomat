
<script>
  import { Octokit } from "octokit";
  import { ref } from 'vue';


  export default {
    setup() {
      const token = import.meta.env.VITE_APP_GH_CRED;
      const octokit = new Octokit({
        auth: token
      });

      const swimLanes = ref([]);
      const projectFields = ref([]);
      const projectCards = ref({});
      const isAuthenticated = ref(false);
      const projects = ref([]);


       // Function to test authentication
       async function testAuthentication() {
        try {
          const response = await octokit.request('GET /user');
          isAuthenticated.value = true;
          console.log('Authenticated as:', response.data);
        } catch (error) {
          isAuthenticated.value = false;
          console.error('Authentication failed:', error);
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
        console.log('Fetched projects:', projects.value);
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
    console.log('GraphQL response:', response); // Log the response for debugging
    const fields = response.node.fields.nodes;
    const items = response.node.items.nodes;

    // Check if the third field is of type ProjectV2SingleSelectField
    if (fields[2].__typename === 'ProjectV2SingleSelectField') {
      const options = fields[2].options;
      console.log('Options for fields.nodes[2]:', options);

      options.forEach(option => {
        swimLanes.value.push(option.name);
        const optionItems = items.filter(item =>
          item.fieldValues.nodes.some(fieldValue =>
            fieldValue.__typename === 'ProjectV2ItemFieldSingleSelectValue' &&
            fieldValue.field.id === fields[2].id &&
            fieldValue.optionId === option.id
          )
        );
        console.log(`Cards for option ${option.name}:`, optionItems);
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
        const owner = import.meta.env.VITE_APP_GH_OWNER; // Replace with your actual repository owner
        const repo = import.meta.env.VITE_APP_GH_REPO; // Replace with your actual repository name
        await fetchProjects(owner, repo);
        if (projects.value.length > 0) {
          await fetchProjectFieldsAndItems(projects.value[0].id);
        }
      }
    }
    fetchData();


      return {
        swimLanes,
        projectFields,
        projectCards,
        projects,
        isAuthenticated
      };

    }
  };
</script>

<template>
  <div class="swimlanes-container">
    <div class="swimlane">
      {{ swimLanes }}
    </div>
    <div class="svg-arrow">
      <svg width="3em" height="3em" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M4 12H20M20 12L16 8M20 12L16 16" stroke="#FFF22F" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" />
      </svg>
    </div>
  </div>
</template>

<style scoped>

  .swimlane {
    color:#FFF22F;
    font-family: 'NOIRetBLANC', serif;
    font-size: 2em;
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

</style>