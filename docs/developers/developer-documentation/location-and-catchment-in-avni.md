---
title: Location and Catchment in Avni
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
**Location Type**

* Example of location type is State, District, and Village. Each a different location type) with a parent location type except for State.
* Each location type has a level. A level is a number. Higher-up location types will have a higher number. So the state, district, and village will have numbers 3,2,1 respectively.

**Location**

* Each location has a location type.
* Location and address\_level are the same things.
* Locations are strictly hierarchical.
* The hierarchy is maintained by the parent\_id column in the address\_level table, location\_location\_mapping, and also via lineage column. Among these three parent\_id columns seems unnecessary.
* location\_location\_mapping is maintained to handle removal of locations from a parent location without deleting the row. In case of delete location\_location\_mapping should be voided and this record is synchronised to offline app so that app can also remove the location from mobile db.
* Lineage column is useful in performing hierarchical queries (as the number of levels are not fixed).

**Catchment**

* The catchment contains a list of locations. The locations can be from different levels also, but usually not.
* Catchment has a list of locations.
* catchment\_address\_mapping is used for maintaining a list of locations within a catchment.
* Ideally this table should be like location\_location\_mapping and have voided field so that instead of deleting a row it can be voided the catchment locations can be synced to the mobile app.
* There is virtual\_catchment\_address\_mapping\_table function (used as read only table in JPA). This provides all the locations (including descedants) in a catchment. This may be redudant as lineage could be used for it.

Subjects are registered to a location.

## Uploading Locations for an Organisation

For an User with "Upload metadata and data" privilege, Avni provides a "Locations" bulk upload feature, which can be used to create a large number of locations at one go.

<Image align="center" src="https://files.readme.io/972587c-Screenshot_2024-05-31_at_4.42.06_PM.png" />

In-addition, through the  "Locations" bulk upload feature, you may also specify additional properties that need to be set for each AddressLevel. These properties could then be used while configuring rules for an entity type's form.

<Image align="center" className="border" border={true} src="https://files.readme.io/b3b6b96-image.png" />

### Location upload modes

Avni supports following modes of Location bulk upload:

1. Create: Used in-order to create a new Location, with or without the Lineage existing before hand. 
2. Edit: Used in-order to edit locations' name, parent(Lineage), GPS coordinates or additional properties.
