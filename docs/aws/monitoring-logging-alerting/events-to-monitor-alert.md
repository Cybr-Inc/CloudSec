---
icon: material/bell-ring
---

# Events to monitor and alert

## Explanation

This is a list of events that were originally [:octicons-link-external-16: posted here](https://github.com/4n6ir/expediate) and that you may want to consider setting up alerts for since they could indicate a security event.

Not all of these may make sense for your environment and they could result in alert fatigue depending on your use case, so use this as a starting point and reference guide, but make sure to think through implementation. Consider also using available monitoring, logging, and alerting tools like the [:octicons-link-external-16: ASSK](https://github.com/zoph-io/aws-security-survival-kit) instead of re-inventing the wheel.

## cloudshell.amazonaws.com
- `CreateEnvironment`
- `CreateSession`
- `DeleteEnvironment`
- `GetEnvironmentStatus`
- `GetFileDownloadUrls`
- `GetFileUploadUrls`
- `PutCredentials`
- `StartEnvironment`
- `StopEnvironment`

## cloudtrail.amazonaws.com
- `DeleteEventDataStore`
- `DeleteTrail`
- `PutEventSelectors`
- `StopLogging`
- `UpdateEventDataStore`
- `UpdateTrail`

## config.amazonaws.com
- `DeleteDeliveryChannel`
- `StopConfigurationRecorder`

## connect.amazonaws.com
- `CreateInstance`

## ec2.amazonaws.com
- `CreateDefaultVpc`
- `CreateImage`
- `CreateInstanceExportTask`
- `CreateKeyPair`
- `CreateVpc`
- `DeleteFlowLogs`
- `DeleteVpc`
- `DescribeInstanceAttribute`
- `DisableEbsEncryptionByDefault`
- `DisableImageBlockPublicAccess`
- `DisableSerialConsoleAccess`
- `DisableSnapshotBlockPublicAccess`
- `EnableEbsEncryptionByDefault`
- `EnableImageBlockPublicAccess`
- `EnableSerialConsoleAccess`
- `EnableSnapshotBlockPublicAccess`
- `GetPasswordData`
- `ModifyInstanceAttribute`
- `ModifySnapshotAttribute`
- `SharedSnapshotCopyInitiated`
- `SharedSnapshotVolumeCreated`

## ecr.amazonaws.com
- `CreateRepository`
- `GetAuthorizationToken`

## ecs.amazonaws.com
- `RegisterTaskDefinition`
- `RunTask`

## eks.amazonaws.com
- `CreateCluster`
- `DeleteCluster`

## elasticache.amazonaws.com
- `AuthorizeCacheSecurityGroupEgress`
- `AuthorizeCacheSecurityGroupIngress`
- `CreateCacheSecurityGroup`
- `DeleteCacheSecurityGroup`
- `RevokeCacheSecurityGroupEgress`
- `RevokeCacheSecurityGroupIngress`

## elasticfilesystem.amazonaws.com

- `DeleteFileSystem`
- `DeleteMountTarget`

## glue.amazonaws.com
- `CreateDevEndpoint`
- `DeleteDevEndpoint`
- `UpdateDevEndpoint`

## guardduty.amazonaws.com
- `CreateIPSet`

## iam.amazonaws.com
- `AddUserToGroup`
- `AttachGroupPolicy`
- `AttachUserPolicy`
- `ChangePassword`
- `CreateAccessKey`
- `CreateLoginProfile`
- `CreateUser`
- `CreateVirtualMFADevice`
- `DeactivateMFADevice`
- `DeleteAccessKey`
- `DeleteUser`
- `DeleteUserPolicy`
- `DeleteVirtualMFADevice`
- `DetachGroupPolicy`
- `DetachUserPolicy`
- `EnableMFADevice`
- `PutUserPolicy`
- `ResyncMFADevice`
- `UpdateAccessKey`
- `UpdateGroup`
- `UpdateLoginProfile`
- `UpdateSAMLProvider`
- `UpdateUser`

## kms.amazonaws.com
- `DisableKey`
- `ScheduleKeyDeletion`

## lambda.amazonaws.com
- `AddLayerVersionPermission`
- `CreateFunction`
- `GetLayerVersionPolicy`
- `PublishLayerVersion`
- `UpdateFunctionConfiguration`

## macie.amazonaws.com
- `DisableMacie`

## macie2.amazonaws.com
- `DisableMacie`

## organizations.amazonaws.com
- `LeaveOrganization`

## rds.amazonaws.com
- `ModifyDBInstance`
- `RestoreDBInstanceFromDBSnapshot`

## rolesanywhere.amazonaws.com
- `CreateProfile`
- `CreateTrustAnchor`

## route53.amazonaws.com
- `DisableDomainTransferLock`
- `TransferDomainToAnotherAwsAccount`

## s3.amazonaws.com
- `PutBucketLogging`
- `PutBucketPublicAccessBlock`
- `PutBucketWebsite`
- `PutEncryptionConfiguration`
- `PutLifecycleConfiguration`
- `PutReplicationConfiguration`
- `ReplicateObject`
- `RestoreObject`

## securityhub.amazonaws.com
- `BatchUpdateFindings`
- `DeleteInsight`
- `UpdateFindings`
- `UpdateInsight`

## sso.amazonaws.com
- `AttachCustomerManagedPolicyReferenceToPermissionSet`
- `AttachManagedPolicyToPermissionSet`
- `CreateAccountAssignment`
- `CreateInstanceAccessControlAttributeConfiguration`
- `CreatePermissionSet`
- `DeleteAccountAssignment`
- `DeleteInlinePolicyFromPermissionSet`
- `DeleteInstanceAccessControlAttributeConfiguration`
- `DeletePermissionsBoundaryFromPermissionSet`
- `DeletePermissionSet`
- `DetachCustomerManagedPolicyReferenceFromPermissionSet`
- `DetachManagedPolicyFromPermissionSet`
- `ProvisionPermissionSet`
- `PutInlinePolicyToPermissionSet`
- `PutPermissionsBoundaryToPermissionSet`
- `UpdateInstanceAccessControlAttributeConfiguration`
- `UpdatePermissionSet`

## sts.amazonaws.com
- `GetFederationToken`
- `GetSessionToken`

## Sources
- [:octicons-link-external-16: Expediate repo](https://github.com/4n6ir/expediate)
- [:octicons-link-external-16: ASSK](https://github.com/zoph-io/aws-security-survival-kit)