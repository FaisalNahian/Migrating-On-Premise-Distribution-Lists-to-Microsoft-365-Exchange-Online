There are a number of ways to migrate on-prem distribution lists to the cloud, but when it comes to a bulk migration or even a simple one or two large group migrations, this process has worked for me and provides some ability to validate your changes before removing the old group completely.

At a high level, this script does the following:

- Create a “placeholder group” in Exchange Online with all attributes populated except the email addresses.
- Export out the current email addresses to a CSV
- Remove the on-premises by filtering it from the sync scope
- Finalize the placeholder group by changing it’s name to the proper name and importing in the email addressees
- Permanently remove the on-premises group by deleting it

Running the Script
1) To create the placeholder group and export the CSV, you’re going to run the following command:

.\Recreate-DistributionGroup.ps1 -Group "DL-Finance" -CreatePlaceHolder

This will create a group in the cloud with the name “Cloud-PreviousDisplayName” and hide it from the GAL.

If it fails, review the error for any duplicate alias that may already exist, resolve the issue, and then re-run the script again.

2) After the placeholder group is created, you will want to validate the new group. You can then delete the old group from Exchange Online by removing it from the Azure AD sync scope. For current versions of AAD Connect, this can be done by populating the “adminDescription” attribute with the value “Group_NoSync”. 

3) Once your sync cycle completes, you can run the following command to make the final cutover:

.\Recreate-DistributionGroup.ps1 -Group "DL-Finance" -Finalize

Assuming everything looks good with the new cloud group, you can delete the on-premises one and setup a mail contact if desired.

Original Author, Joe Palarchio, posted this script in the Microsoft Script Center but that is no longer available. 
