# Jenkins : User Management

- After INstalling Jenkins and the Suggested Plugins that normally Jenkins reccomand you now it's time toc reate and manage users  and give them permissions :

***

## Create Users

1- Navigate to User Management:

- Log in as admin
- Click on "Manage Jenkins" in the left sidebar
- `Ctrl+F` and search *Users* then click on it
- Click on **Create User**
- Fill the user informations :
![Create User](/img/2-Create-User.png)

***

## Basic Authentication and Authorization

By default, Jenkins uses its own user database for authentication. For more complex permission management, you'll need to install the "Role-based Authorization Strategy" plugin:

1- Install the Plugin:

- Go to "Manage Jenkins" → "Manage Plugins"
- Click on the "Available" tab
- Search for `"Role-based Authorization Strategy"`
- Check the box next to it and click "Install without restart"

2- Enable Role-Based Strategy:

- Go to "Manage Jenkins" → "Configure Global Security"
- Under `"Authorization"`, select "Role-Based Strategy"
- Click "Save"

***

## Managing Roles and Permissions

After installing the plugin and enabling it:

1- Access Role Management:

- Go to "Manage Jenkins"
- Click on `"Manage and Assign Roles"` (this appears after installing the plugin)

2- Creating Global Roles:

- Under "Global roles", enter a role name (e.g., "Developer", "Tester", "Admin")
- Check the permissions you want to grant to this role
- Click "Save"

![Manage Roles](/img/3-Roles.png)

3- Creating Project-Specific Roles:

- In the same "Manage Roles" page
- Under "Project roles", enter a role name
- Specify a pattern (e.g., "dev-.*" to match all jobs starting with "dev-")
- Check relevant permissions
- Click "Save"

4- Assigning Roles to Users:

- Go back to "Manage and Assign Roles"
- Click on "Assign Roles"
- Under "Global roles", check the role(s) for each user
- Under "Item roles", assign project-specific roles
- Click "Save"

![Assign Roles](/img/4-userrole.png)
