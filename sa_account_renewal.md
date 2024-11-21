### Documentation: Rotating and Updating the Service Account Password for Terraform Enterprise (TFE) with External PostgreSQL (SA in Active Directory)

This guide outlines the steps to rotate and update the service account (SA) password used by Terraform Enterprise (TFE) to connect to an external PostgreSQL database. The service account is managed in Active Directory, and password rotation is performed using **CyberArk**. This documentation is specific to TFE Replicated version `v202109-1`.

---

### Prerequisites
1. **CyberArk Access**: Ensure you have access to CyberArk to manage and rotate the password for the Active Directory (AD) service account.
2. **Administrative Permissions**: Confirm you have administrative permissions for TFE and access to the PostgreSQL database.
3. **Service Account Details**:
   - Service account username in AD.
   - Database details (PostgreSQL hostname, port, and database name).
4. **Backup**: Take a backup of:
   - TFE settings.
   - PostgreSQL database.

---

### Steps to Rotate and Update the Password

#### Step 1: Rotate the Password in CyberArk
1. **Log in to CyberArk**:
   - Access your CyberArk dashboard.

2. **Locate the Service Account**:
   - Search for the service account (SA) associated with TFE and PostgreSQL in CyberArk.

3. **Rotate the Password**:
   - Use the **Rotate Password** feature in CyberArk to generate a new strong password for the service account.
   - Save the new password securely for use in subsequent steps.

---

#### Step 2: Validate and Sync the New Password with Active Directory
1. Confirm that the new password has been successfully synchronized with the Active Directory (AD) service account.
2. If necessary, test the updated credentials using a secure tool (e.g., `psql` or another database client) to verify connectivity to PostgreSQL:
   ```bash
   psql -h <postgres_host> -U <service_account> -d <database_name>
   ```

---

#### Step 3: Update the Password in Terraform Enterprise
1. **Access the TFE Admin Console**:
   - Navigate to the TFE Replicated Admin Console at `https://<TFE_instance>:8800`.

2. **Pause Terraform Enterprise**:
   - Under **Settings**, select **Stop Application** to safely pause TFE operations.

3. **Update PostgreSQL Connection Details**:
   - Go to **Settings > Data Storage**.
   - Edit the PostgreSQL connection string to include the new password:
     ```plaintext
     postgres://<service_account>:<new_password>@<postgres_host>:<port>/<database_name>
     ```

4. **Save the Changes**:
   - Click **Save** to apply the updated connection details.

5. **Restart Terraform Enterprise**:
   - Under **Settings**, select **Start Application** to resume TFE operations.

---

#### Step 4: Test and Validate the Connection
1. **Verify Connectivity**:
   - Log in to TFE and ensure there are no connection errors.
   - Run a test Terraform plan to confirm TFE can access the PostgreSQL database.

2. **Check Logs**:
   - Monitor the TFE logs for errors:
     ```bash
     docker logs -f replicated
     ```

---

#### Step 5: Confirm Password Rotation and Document the Change
1. **Monitor Stability**:
   - Observe TFE for a short period to ensure all operations run smoothly.

2. **Document the Change**:
   - Update internal documentation or ticketing systems with the date, time, and reason for the password rotation.

3. **Invalidate the Old Password**:
   - Once the new password is confirmed to be working, ensure the old password is fully invalidated as per security policies.

---

### Additional Notes
- If TFE fails to connect after the password update:
  - Double-check the connection string in the TFE admin console.
  - Confirm the new password in CyberArk matches the one in use.
  - Test direct connectivity to PostgreSQL using the new credentials.

- Work closely with your AD and CyberArk administrators to ensure compliance with organizational security policies.

This process ensures a secure and seamless password rotation for the Terraform Enterprise service account.