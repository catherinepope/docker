# Role-Based Access Control (RBAC)

RBAC is performed by creating grants. The grant consists of:

1. Subject (person or team)
2. Collection (resources)
3. Role (privileges)

The **subject** is the team, or one of more users, who will access a certain collection/resource with granular roles/privileges.

You can make the grant and its components using the UCP or the command line.

RBAC works the same way in Docker and Kubernetes.

1. Create users
2. Create a collection (you can include or exclude children/grandchildren)
3. Create a role
4. Create a grant

A **grant** defines who (subject) has how much access (role) to a set of resources (collection). Each grant is a 1:1:1 mapping of subject, role, and collection.

For example the *masters* team has all to all *secret* operations in the *kubeadmins* collection.