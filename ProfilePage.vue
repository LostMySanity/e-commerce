<template>
    <div>
        <vx-card title="Profile" class="mb-base">
            <div class="vx-row">
                <div class="vx-col">
                    <div class="img-container mb-4">
                        <img :src="user_data.img" class="rounded w-full" />
                    </div>
                </div>
                <div class="vx-col flex-1" id="account-info-col-1">
                    <table>
                        <tr>
                            <td class="font-semibold">Username</td>
                            <td>{{ user_data.username }}</td>
                        </tr>
                        <tr>
                            <td class="font-semibold">Name</td>
                            <td>{{ user_data.name }}</td>
                        </tr>
                        <tr>
                            <td class="font-semibold">Email</td>
                            <td>{{ user_data.email }}</td>
                        </tr>
                    </table>
                </div>
                <div class="vx-col flex-1">
                    <table>
                        <tr>
                            <td class="font-semibold">Status</td>
                            <td>{{ user_data.status }}</td>
                        </tr>
                        <tr v-if="!user_data.role == 'user'">
                            <td class="font-semibold">Role</td>
                            <td>{{ user_data.role }}</td>
                        </tr>
                        <tr>
                            <td class="font-semibold">Birth Date</td>
                            <td>{{ user_data.dob }}</td>
                        </tr>
                        <tr>
                            <td class="font-semibold">Mobile</td>
                            <td>{{ user_data.mobile }}</td>
                        </tr>
                        <tr>
                            <td class="font-semibold">Gender</td>
                            <td>{{ user_data.gender }}</td>
                        </tr>
                        <tr>
                            <td class="font-semibold">Address</td>
                            <td>{{ user_data.address }}</td>
                        </tr>
                    </table>
                </div>
            </div>
        </vx-card>
    </div>
</template>

<script>

export default {

    created:{
        
    }
}
</script>