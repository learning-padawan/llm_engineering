Get Started with Modeling
Creating a Relationship Based Access Control (ReBAC) authorization model might feel odd at first. Most of us tend to think about authorization models in terms of roles and permissions. After all, most software works like that. Your existing systems are likely built on a model using roles and permissions.

This guide outlines a process for defining your authorization model with OpenFGA.

Introduction To Modeling
To define a ReBAC model in OpenFGA we recommend:

If you have an existing system: forget about how your system works today and start thinking about how you want it to work in the future.
Thinking about authorization starting from the resources, or objects as OpenFGA calls them.
If that sounds hard, don't worry! We'll guide you through it.

OpenFGA is built to quickly and reliably make authorization checks . This means providing an answer to a question: "Can user U perform action A on object O?"

ReBAC systems determine access from a user's relation to an object. Authorization decisions are then yes or no answers to the question: "Does user U have relation R with object O?".

General Authorization Check
"Can user U perform an action A on object O?"

OpenFGA (ReBAC) Authorization Check
"Does user U have relation R with object O?"

In the previous example, a relation R should be defined that implies permission to action A. For example:

General Authorization Check
"Can user Jane perform action view on object project sandcastle?"

OpenFGA (ReBAC) Authorization Check
"Can user Jane have relation view with object project sandcastle?"

We'll provide more detailed examples throughout this article.

When you are modeling, you need to answer a more general question:

Why could user U perform an action A on an object O?
If you can answer that question for all types of objects in your system, then you can codify that into an authorization model.

Let's get started!

A Process For Defining Authorization Models
Defining an authorization model requires codifying an answer to the question "why could user U perform an action A on an object O?" for all use cases or actions in your system. This is an iterative process. For the purpose of this guide, we'll go through one iteration of this process using a simplified Google Drive like system as an example.

Steps for defining your authorization model:

Pick the most important feature
List the object types
List relations for those types
Define relations
Test the model
Iterate
The starting point

1.  Pick The Most Important Feature
    Pick the most important feature

A feature, in the context of this document, is an action or related set of actions your users can perform in your system. We'll introduce an example feature later in this section.

Start with the most important feature. It doesn't have to be the most complex one, but it should be the most important one. You're probably more familiar with the authorization requirements for this feature than other less important use cases.

Important
Requirement clarity is fundamental when defining an authorization model.
The scope of the feature is not important at this point. You can always iterate later.
Write It In Plain Language
Once you've picked a feature, describe its authorization related scope using simple language. Avoid using the word "roles", as this ties you to an RBAC way of thinking.

info
Roles don't "disappear" in ReBAC systems like OpenFGA. Your users might have roles on a given object, rather than the entire system. But starting from the term "role" might lead you down the wrong path. Instead it is better to discover roles while you are modeling.

Your feature description should include the objects, users and groups of users participating in the system. Sentences should look like this:

A user {user} can perform action {action} to/on/in {object types} ... IF {conditions}
Let's look at an example of a simplified Google Drive like system. We'll focus on the feature allowing users to create, read, update, delete, and share documents with other users.

This feature can be described with these sentences:

A user can create a document in a drive if they are the owner of the drive.
A user can create a folder in a drive if they are the owner of the drive.
A user can create a document in a folder if they are the owner of the folder. The folder is the parent of the document.
A user can create a folder in a folder if they are the owner of the folder. The existing folder is the parent of the new folder.
A user can share a document with another user or an organization as either editor or viewer if they are an owner or editor of a document or if they are an owner of the folder/drive that is the parent of the document.
A user can share a folder with another user or an organization as a viewer if they are an owner of the folder.
A user can view a document if they are an owner, viewer or editor of the document or if they are a viewer or owner of the folder/drive that is the parent of the document.
A user can edit a document if they are an owner or editor of the document or if they are an owner of the folder/drive that is the parent of the document.
A user can change the owner of a document if they are an owner of the document.
A user can change the owner of a folder if they are an owner of the folder.
A user can be a member of an organization.

How a user is added as a member to an organization is beyond the scope of the feature we picked to write down.

A user can view a folder if they are the owner of the folder, or a viewer or owner of either the parent folder of the folder, or the parent drive of the folder. 02. List The Object Types
List the object types

Next make a list of the types of objects in your system. You might be able to identify the objects in your system from your existing domain/database model.

Find all the objects in the previous step using this template:

A user {user} can perform action {action} to/on/in {object type} ... IF {conditions}
These are all the object types from the previous step (in order of appearance) based on that template:

Document

Folder

Organization

Let's highlight all object types in blue:

A user can create a document in a drive if they are the owner of the drive.
A user can create a folder in a drive if they are the owner of the drive.
A user can create a document in a folder if they are the owner of the folder.
A user can create a folder in a folder if they are the owner of the folder.
A user can share a document with another user or an organization as either editor or viewer if they are an owner or editor of a document or if they are an owner of the folder/drive that is the parent of the document.
A user can share a folder with another user or an organization as a viewer if they are an owner of the folder.
A user can view a document if they are an owner, viewer or editor of the document or if they are a viewer, owner of the folder/drive that is the parent of the document.
A user can edit a document if they are an owner or editor of the document or if they are an owner of the folder/drive that is the parent of the document.
A user can change the owner of a document if they are an owner of the document.
A user can change the owner of a folder if they are an owner of the folder.
A user can be a member of an organization.

How a user is added as a member to an organization is beyond the scope of the feature we picked to write down.

A user can view a folder if they are the owner of the folder, or a viewer or owner of either the parent folder of the folder, or the parent drive of the folder.
However, the list of object types is not finished. To complete the list of object types you must also add all the second nouns that appear in conditions as part of expressions of this format: "{first noun} of a/the {second noun}".

... IF {first noun} of a/the {second noun}
Let's highlight those expressions in green:

A user can create a document in a drive if they are the owner of the drive.
A user can create a folder in a drive if they are the owner of the drive.
A user can create a document in a folder if they are the owner of the folder. The folder is the parent of the document.
A user can create a folder in a folder if they are the owner of the folder. The existing folder is the parent of the new folder .
A user can share a document with another user or an organization as either editor or viewer if they are an owner or editor of a document or if they are an owner of the folder/drive that is the parent of the document.
A user can share a folder with another user or an organization as a viewer if they are an owner of the folder.
A user can view a document if they are an owner, viewer or editor of the document or if they are a viewer or owner of the folder/drive that is the parent of the document.
A user can edit a document if they are an owner or editor of the document or if they are an owner of the folder/drive that is the parent of the document.
A user can change the owner of a document if they are an owner of the document.
A user can change the owner of a folder if they are an owner of the folder.
A user can be a member of an organization.

How a user is added as a member to an organization is beyond the scope of the feature we picked to write down.

A user can view a folder if they are the owner of the folder, or a viewer or owner of either the parent folder of the folder, or the parent drive of the folder.
The only second noun we didn't have in our object type list is "Drive", so we'll add it to the list. We will also need to add "User" to the list as it establishes the type of user who can establish relations.

User

Document

Folder

Organization

Drive

Now that we have a list of object types we can start defining them using the OpenFGA Configuration Language:

model
schema 1.1

type user

type document

type folder

type organization

type drive

Caution
You're now in the process of building a version you can use. The model above is not yet a valid authorization model accepted by OpenFGA.

Important
In a few cases other users can be part of determining whether an action can be performed on an object or not. Social media is an example of this "a user can comment on a picture if they are a friend of the user that published it".

In those cases User should also be an object type. Following the last recommendation, we would discover the User type because it is a second noun in an expression: "friend of the user".

3.  List Relations For Those Types
    List relations for those types

Each of the previously defined types has a set of relations. Relations are an important component in your model. After all, OpenFGA is a Relationship Based Access Control (ReBAC) system.

To identify relations for a type in the write-up we can perform an exercise similar to the one we did in list the type of objects in your system.

Relations for a type {type} will be all of these:

any noun that is the {noun} of a "{noun} of a/an/the {type}" expression. These are typically the Foreign Keys in a database. We'll highlight these in green.
any verb or action that is the {action} of a "can {action} (in) a/an {type}" expression. These are typically the permissions for a type. We'll highlight these in yellow.
A user can create a document in a drive if they are the owner of the drive.
A user can create a folder in a drive if they are the owner of the drive.
A user can create a document in a folder if they are the owner of the folder. The folder is the parent of the document.
A user can create a folder in a folder if they are the owner of the folder. The existing folder is the parent of the new folder.
A user can share a document with another user or an organization as either editor or viewer if they are an owner or editor of a document or if they are an owner of the folder/drive that is the parent of the document.
A user can share a folder with another user or an organization as a viewer if they are an owner of the folder.
A user can view a document if they are an owner, viewer or editor of the document or if they are a viewer or owner of the folder/drive that is the parent of the document.
A user can edit a document if they are an owner or editor of the document or if they are an owner of the folder/drive that is the parent of the document.
A user can change the owner of a document if they are an owner of the document.
A user can change the owner of a folder if they are an owner of the folder.
A user can be a member of an organization.

How a user is added as a member to an organization is beyond the scope of the feature we picked to write down.

A user can view a folder if they are the owner of the folder, or a viewer or owner of either the parent folder of the folder, or the parent drive of the folder.
The resulting list is:

Document

parent
can_share
owner
editor
can_write
can_view
viewer
can_change_owner
Folder

can_create_document
owner
can_create_folder
can_view
viewer
parent
Organization

member
Drive

can*create_document
owner
can_create_folder
info
In OpenFGA, relations can only have alphanumeric characters, underscores and hyphens. We recommend using underscore (*) to separate words and removing prepositions. E.g.: "can create a document" can become "can_create_document" or "create_document" if you are into brevity.

Using the OpenFGA Configuration Language we can enumerate the relations for each type:

model
schema 1.1
type user
type document
relations
define parent:
define owner:
define editor:
define viewer:
define can_share:
define can_view:
define can_write:
define can_change_owner:
type folder
relations
define owner:
define parent:
define viewer:
define can_create_folder:
define can_create_document:
define can_view:
type organization
relations
define member:
type drive
relations
define owner:
define can_create_document:
define can_create_folder:

Caution
You're now in the process of building a version you can use. The model above is not yet a valid authorization model accepted by OpenFGA.

4.  Define Relations
    Define relations

We will use the OpenFGA Configuration Language to create a relation definition for each of the relations we identified. At this stage we will encode the answers to the question we asked at the beginning of the document:.

Why could a user U, perform an action A on an object O?
We are going to go over each type and each of its relations and create a definition for it.

Type: Organization
We recommend starting from objects that represent groups/containers of users. For features in most systems these are easy to define and help reason about the other types. Examples of type names for these are "team", "group", "organization", etc.

Relation: Member
The member relation is used to tell OpenFGA about the members of an organization.

Important
Relation names in OpenFGA are arbitrary strings. There are no reserved relation names. You can use "member" or "part_of" or anything else to refer to a user that is part of a team/organization.

Remember "How a user is added as a member to an organization is beyond the scope of this feature." For the purposes of this model the relation definition should be:

type organization
relations
define member: [user, organization#member]

Why? This relation definition states:

That organizations have members

That the members of an organization with id {id} are all users described by tuples of the form:

{ user: {user-id}, relation: "member", object: "organization:{id}" }

Important
Relation definitions of the form "define {relation}: [user, organization#member]" are fairly common. They are used to express that relationships "to the object with that relation" (e.g. "users" of type user or "member of organization") can be assigned by your system and that only the users that have that relation are those with a direct relationship.

You can read more about group membership and types in Modeling User Groups.

For the direct relationships, we need to figure out the object types that makes sense for the relationship tuples' user. In our organization example, it makes sense for member relations to have user of type

user
organization#member (i.e., other organization's member)
However, it will not make sense for organization member's user to be of type document, folder or drive.

We will specify this logic as part of directly related user type.

Side note
This also automatically supports nested organizational membership if you want such a feature in your system. You could use relationship tuples like the following one to express that "members of organization A are members of organization B":

{ user: "organization:A#member", relation: "member", object: "organization:B"}

If you want to learn more, you can read further about this in Modeling User Groups and Managing Relationships Between Objects.

Complete Type Definition
The complete type definition for the organization type is:

type organization
relations
define member: [user, organization#member]

Type: Document
After defining your "group" like types, continue with the most important type for the feature: the one that allows the main use case. In this case "document", since the main use case for users is to create, write, read and collaborate on documents.

Defining relations for the main type lets you focus on your core use case, and will likely make other type definitions easier.

Relation: Owner
The owner relation is used to tell OpenFGA which users are owners of the document.

Important
In the current version, there is no way to state that there is only one owner in the authorization model. The application must limit this set of users to just one owner if that is a requirement.

When a document is created, a relationship tuple will be stored in OpenFGA representing this relationship between owner and document. This is an example of a user to object relationship.

The relation definition then should be:

type document
relations
define owner: [user, organization#member]

Why? This relation definition states that:

each document can have one or more owners
owners of a document are assignable by creating a tuple of the format { user: "{user_id}", relation: "owner", object: "document:{id}" } for individual users
Relation: Editor
The editor relation is used to tell OpenFGA which users are editors of the document.

When a user shares a document with another user or set of users as editor, a relationship tuple will be stored in OpenFGA representing this relationship between editor and document. This is an example of a users to object relationship.

The relation definition then should be:

type document
relations
define editor: [user, organization#member]

Why? This relation definition states that:

each document can have editors
the editor(s) of a document are assignable by creating a tuple with shape { user: "{user_id}", relation: "editor", object: "document:{id}" } for individual users
This also supports making all members in an organization editors of the document, through a group to object relationship. A relationship tuple like the following one states that the members of organization A are editors of document 0001.

[{
"user": "organization:A#member",
"relation": "editor",
"object": "document:0001"
}]

You can learn more about this in Modeling User Groups.

Relation: Viewer
The viewer relation is similar to the document's editor relation. It will be defined like this:

type document
relations
define viewer: [user, organization#member]

Relation: Parent
The parent relation is used to tell OpenFGA which folder or drive is the parent of the document.

Important
Relation names in OpenFGA are arbitrary strings. There are no reserved relation names. You can use "parent", "container" or "ancestor" to refer to a "parent folder".

This relation is different from the others we have seen so far, as it is a relation between two objects (a folder and or drive that is the parent of the document). This is known as an object to object relationship, of which parent-child is a particular case.

When a document is created a relationship tuple will be stored in OpenFGA to represent this relationship between parent and document. The relation definition then should be:

type document
relations
define parent: [folder, drive]

Why? This relation definition states that:

documents may have a parent
the parent(s) of a document with id {id} is either a folder or a drive, described by one of these relationship tuples:
{ user: "folder:{id}", relation: "parent", object: "document:{id}" }
{ user: "drive:{id}", relation: "parent", object: "document:{id}" }
We can use direct type restriction to ensure a document's parent can only be an object of type either drive or folder.

Side note
You might have noticed that the "user" in the tuple is an object. This is a special syntax OpenFGA accepts in the "user" parameter to write object to object relationships. You can read more about writing data to manage object to object relationships in Managing Relationships Between Objects.

Relation: can_share
We need to express the following in the relation definition:

A user can share a document with another user or an organization as either editor or viewer if they are an owner or editor of a document or if they are an owner of the folder that is the parent of the document.

We can achieve that with the following definition using OpenFGA Configuration Language:

type document
relations
define can_share: owner or editor or owner from parent

There are a few key things here:

We don't use a direct relationship type restriction as part of the definition. can_share is a common example of representing a permission that is defined in terms of other relations but is not directly assignable by the system.
The relation definition contains a union operator separating a list of relations that the user must have with the object in order to "be able to share the document". It is any of:
Being an owner of the document
Being an editor of the document
Being an owner of the parent of the document. Whether the parent is a drive or a folder is not important, as they both have an owner relation.
You can read more about the aforementioned items in Modeling Roles and Permissions.

Relation: can_view
We need to express the following in the relation definition:

A user can view a document if they are an owner, viewer or editor of a document or if they are a viewer, owner of the folder/drive that is the parent of the document.

Similar to the can_share relation, we can achieve that with the following definition using OpenFGA Configuration Language:

type document
relations
define can_view: viewer or editor or owner or viewer from parent or owner from parent

Relation: can_write
We need to express the following in the relation definition:

A user can write a document if they are an owner or editor of a document or if they are an owner or editor of the folder/drive that is the parent of the document.

Similar to the can_share relation, we can achieve that with the following definition using OpenFGA Configuration Language:

type document
relations
define can_write: editor or owner or owner from parent

Relation: can_change_owner
We need to express the following in the relation definition:

A user can change the owner of a document if they are an owner of the document.

Similar to the can_share relation, we can achieve that with the following definition using OpenFGA Configuration Language:

type document
relations
define can_change_owner: owner

Complete Type Definition
The complete type definition for the document type is:

model
schema 1.1

type document
relations
define owner: [user, organization#member]
define editor: [user, organization#member]
define viewer: [user, organization#member]
define parent: [folder]
define can_share: owner or editor or owner from parent
define can_view: viewer or editor or owner or viewer from parent or editor from parent or owner from parent
define can_write: editor or owner or owner from parent
define can_change_owner: owner

Combining the type definitions for document and organization, we have

model
schema 1.1

type user

type organization
relations
define member: [user, organization#member]

type document
relations
define owner: [user, organization#member]
define editor: [user, organization#member]
define viewer: [user, organization#member]
define parent: [folder]
define can_share: owner or editor or owner from parent
define can_view: viewer or editor or owner or viewer from parent or editor from parent or owner from parent
define can_write: editor or owner or owner from parent
define can_change_owner: owner

note
The OpenFGA authorization model API and SDK only accepts JSON in its input. To convert from DSL to JSON, you may use the FGA CLI to run fga model transform.

5.  Test The Model
    Test the model

Once you have defined your group like types and the most important type for your feature you want to ensure everything is working as expected. This means testing the model.

How? Remember from the introduction that OpenFGA's main job is to answer the question:

Can user U, perform an action A on an object O?
The OpenFGA service does that by checking if a user has a particular relationship to an object, based on your authorization model and relationship tuples.

General Authorization Check
"Can user U perform action A on object O?"

OpenFGA (ReBAC) Authorization Check
"Can user U have relation R with object O?"

What we want is to ensure that given our current authorization model and some sample relationship tuples, we get the expected results for those questions.

So we'll write some relationship tuples and assertions. An OpenFGA assertion takes one of these forms:

user U has relation R with object O
user U does not have relation R with object O
Much like automated tests and assertions work for programming languages, you can use assertions to prevent regressions while you change your tuples and authorization model. Essentially, assertions help you ensure things work like you expect them to work as you iterate.

Write Relationship Tuples
The relationship tuples should represent real examples from your system with fake data.

At this point you haven't defined the drive or folder types, so you can only test things based on users or organization members' relationships to documents. Let's imagine an example setup and write the relationship tuples for it:

System Action Relationship Tuple
Anne is a member of the contoso organization { user:"user:anne", relation: "member", object: "organization:contoso"}
Beth is a member of fabrikam organization { user:"user:beth", relation: "member", object: "organization:fabrikam"}
Anne creates document:1, becomes its owner. { user:"user:anne", relation: "owner", object: "document:1"}
Anne shares document:1 with all members of the fabrikam organization as editor. { user:"organization:fabrikam#member", relation: "editor", object: "document:1"}
Beth creates document:2 and becomes its owner. { user:"user:beth", relation: "owner", object: "document:2"}
Beth shares document:2 with all members of the contoso organization as viewer { user:"organization:contoso#member", relation: "viewer", object: "document:2"}
Follow these steps to create relationship tuples.

Create Assertions
According to our written down model and the relationship tuples from the previous step, these assertions should be specified:

Because anne is the owner of document:1:

user anne has relation can_share with document:1
user anne has relation can_write with document:1
user anne has relation can_view with document:1
user anne has relation can_change_owner with document:1
Because beth is a member of organization:fabrikam and members of organization:fabrikam are writer of document:1:

user beth has relation can_share with document:1
user beth has relation can_write with document:1
user beth has relation can_view with document:1
user beth does not have relation can_change_owner with document:1
Because beth is the owner of document:2:

user beth has relation can_share with document:2
user beth has relation can_write with document:2
user beth has relation can_view with document:2
user beth has relation can_change_owner with document:2
Because anne is a member of organization:contoso and members of organization:contoso are viewer of document:2:

user anne does not have relation can_share with document:2
user anne does not have relation can_write with document:2
user anne has relation can_view with document:2
user anne does not have relation can_change_owner with document:2
Follow these steps to create assertions.

Run Assertions
Run the assertions. They should all pass. If they don't you can use the query view to understand what is causing them to fail, and then update your authorization model and relation tuples accordingly.

Once all the assertions are working, you should continue the iterative process of working on your model.

6.  Iterate
    Iterate

We'll leave the exercise of defining the drive and folder relations, then adding relationship tuples and assertions to you. Once you are finished, check out the complete example to see how you did.

When defining the authorization model for your own system, you would continue iterating on the authorization model with the next feature and so on.
