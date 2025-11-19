<script setup>
const menuList = [
  { title: "Action" },
  { title: "Another Action" },
  { title: "Something elese here" },
];

const tableHeader = ["", "Name", "Deadline", "Status"];

const tableData = [
  {
    id: 1,
    name: "Design a FreshCart Home page",
    deadline: "Today",
    status: 1,
  }
];

const customers = async () => {
  // errorMessage.value = '';
  const config = useRuntimeConfig();
  try {
    const response = await fetch(config.public.apiBase+ '/customers', {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json',
      },
    });

    if (!response.ok) {
      const errorData = await response.json();
      throw new Error(errorData.message || 'Signup failed');
    }

    // Handle successful signup (e.g., redirect to login or dashboard)
    return await response.json();
    // Example: navigateTo('/login');
  } catch (error) {
    console.error('Signup error:', error);
    // errorMessage.value = error.message;
  }
};

const data = await customers();

console.log(data);

const resolveStatusVariant = (status) => {
  if (status === 1)
    return {
      color: "success",
      text: "Approved",
    };
  else if (status === 2)
    return {
      color: "warning",
      text: "In Progress",
    };
  else
    return {
      color: "error",
      text: "Pending",
    };
};
</script>

<template>
  <v-card class="h-100">
    <v-card-title class="d-flex align-center justify-space-between">
      <h4 class="text-h4">Danh sách khách hàng </h4>
      <v-menu location="bottom">
        <template v-slot:activator="{ props }">
          <v-btn
            v-bind="props"
            class="text-button"
            size="small"
            variant="outlined"
            color="secondary"
          >
            Task <v-icon icon="tabler-chevron-down" />
          </v-btn>
        </template>
        <v-list>
          <v-list-item v-for="(item, i) in menuList" :key="i" :value="item.title">
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-card-title>
    <v-divider />
    <v-table hover>
      <thead>
        <tr>
          <th v-for="item in tableHeader" :key="item">
            {{ item }}
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in tableData" :key="item.name">
          <td class="w-36">
            <v-checkbox />
          </td>
          <td class="min-w-65">
            {{ item.name }}
          </td>
          <td class="min-w-37">
            {{ item.deadline }}
          </td>
          <td>
            <v-chip
              size="x-small"
              class="font-weight-6"
              :color="resolveStatusVariant(item.status).color"
              label
            >
              {{ resolveStatusVariant(item.status).text }}
            </v-chip>
          </td>
        </tr>
      </tbody>
    </v-table>
  </v-card>
</template>
