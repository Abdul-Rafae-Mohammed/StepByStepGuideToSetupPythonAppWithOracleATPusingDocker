<table class="tbl-heading"><tr><td class="td-logo">[![](images/obe_tag.png)](README.md)

Last Updated:<br>November 06, 2019
</td>
<td class="td-banner">
# Guide 1: Creating a REST Service on Oracle ATP Database using APEX
</td></tr><table>

## Introduction

Application Express (APEX) is a great tool to build Applications, REST API's on Oracle Database. It enables us to creates REST services which can be a single point of access for Developers. The DBA's can provide access to users like developers without even having to share the connection details adding more security to the database.

In this guide , you are going to create a REST service which you will use in the next guide , to send data from a Python Application into Oracle Database.


To **log issues**, click [here](https://github.com/Abdul-Rafae-Mohammed/StepByStepGuideToSetupPythonAppWithOracleATP/issues/new) to go to the github oracle repository issue submission form.

## Objectives

- Learn how to build REST services using APEX.

## Required Artifacts

- A pre-provisioned Oracle ATP database instance.

  Learn more about provisioning and using Oracle ATP by visiting [ATP Documentation](https://docs.oracle.com/en/cloud/paas/atp-cloud/atpug/getting-started.html#GUID-F370F478-2B17-48EA-A7C0-BCE0C1E3C72F) website


## Steps

### **STEP 1: Log into APEX and create a new workspace**

- Log into your APEX account using the link provided in the ATP Database console connection page.

    - **password**: WElCome12_34# \<Database Admin Password\>
    
    **This is the same password you used when you provisioned the ATP instance.**

  ![](./images/300/Picture300-1-1.png)
  ![](./images/300/Picture300-1.png)
  

- Click **Create Workspace** button.

  ![](./images/300/Picture300-2.png)

- Enter Workspace Name and click **next** button.

  ![](./images/300/Picture300-3.png)

- Select YES from the dropdown for Re-use existing schema.
- Select the schema we created earlier from the list.

  ![](./images/300/Picture300-4.png)

- Click **next**.

  ![](./images/300/Picture300-4-1.png)

- enter admin password
- enter first name and last name for the apex user.
- enter the email.
- click **next**.

  ![](./images/300/Picture300-5.png)

- Click **Create Workspace** and click **Done**.

  ![](./images/300/Picture300-6.png)

- Now logout and login using the workspace we just created.

  ![](./images/300/Picture300-7.png)

- Click **Register schema with ORDS**  button, if prompted.
  ![](./images/300/Picture300-7-0.png)
  ![](./images/300/Picture300-7-1.png)

- A Pop-up window opens up. Select yes for enable restful services option, select  No for Install Sample service and select No for authorization required option. Click **Save Schema Attributes**.

  ![](./images/300/Picture300-7-2.png)

- Click on SQl Workshop.

  ![](./images/300/Picture300-8.png)

- Click on RESTful Services.

  ![](./images/300/Picture300-9.png)

- Click on **Modules** on the left panel.

  ![](./images/300/Picture300-10.png)

- Click **Create Module** on top right of the screen.
- Enter the Module name and the Base Path of your choice. Click **Create Module** button.

  ![](./images/300/Picture300-11.png)

- Now, on the Module Definition page. Click **Create Template** on right side of the screen.

  ![](./images/300/Picture300-12.png)

- On the Template Definition Page, enter the URI template of your choice. Click **Create Template**.

  ![](./images/300/Picture300-13.png)

- Now you will see a message on the top of the screen confirming that the Template is created.

- Now, click **Create Handler** button on right side of the screen.

  ![](./images/300/Picture300-14.png)

- On the Handler Definition page, select the **Method** as GET and select the **Source Type** as PL/SQL from the dropdown.

-  Now, enter the below PL/SQL procedure in the **source** section below the comments box.

    ```
    begin
      apex_json.open_array;
      for c in ( select * from jsontweets ) loop
        apex_json.open_object;
          apex_json.write( 'timestamp', to_char( c.ts, 'yyyy/mm/dd hh24:mi:ss') );
          apex_json.write( 'tweet', c.tweetjson );
        apex_json.close_object;
      end loop;
      apex_json.close_array;
    end;
    ```

- Now, click **Create Handler** button on top right of the page.

  ![](./images/300/Picture300-15.png)

- Now, click on the template you created from the panel on the left side.

  ![](./images/300/Picture300-16.png)

- Now, click on **Create Hendler** button on right side of the screen.

  ![](./images/300/Picture300-18.png)

- Select the **Method** to be POST and copy the below PL/SQL procedure

  ```
  begin
    insert into jsontweets values (sysdate, :data);
    commit;
  end;
  ```

  ![](./images/300/Picture300-17.png)

- Scroll the page down and click **Add Row** in the **Parameter** section and enter the following details.

    - Name : data
    - Bind Variable : data
    - Source Type : HTTP Header
    - Access Method : IN
    - Data Type : STRING

  ![](./images/300/Picture300-17-1.png)

- Scroll back up and click **Create Handler**.

- Copy the REST Endpoint by clicking on the copy button beside the Full URL on the Handler's Definition Page. 

  ![](./images/300/Picture300-19.png)

- Now, your REST Service is ready!

## Alternatively, You can also use the below PL/SQL Script to configure the REST Service we just configured, then just login to APEX and copy the full URL of the Service. 

### Note : Please Make sure you make necessary changes (indicated in the angular braces) to the script before executing it.

      ```
      BEGIN
      ORDS.ENABLE_SCHEMA(
          p_enabled             => TRUE,
          p_schema              => '<Schema Name>',
          p_url_mapping_type    => 'BASE_PATH',
          p_url_mapping_pattern => '<Workspace Name>',
          p_auto_rest_auth      => FALSE);
        
      ORDS.DEFINE_MODULE(
          p_module_name    => '<Module Name>',
          p_base_path      => '/<Base Path>/',
          p_items_per_page => 25,
          p_status         => 'PUBLISHED',
          p_comments       => NULL);

      ORDS.DEFINE_TEMPLATE(
          p_module_name    => '<Module Name>',
          p_pattern        => '<Pattern Name>',
          p_priority       => 0,
          p_etag_type      => 'HASH',
          p_etag_query     => NULL,
          p_comments       => NULL);

      ORDS.DEFINE_HANDLER(
          p_module_name    => '<Module Name>',
          p_pattern        => '<Pattern Name>',
          p_method         => 'GET',
          p_source_type    => 'json/collection',
          p_mimes_allowed  => '',
          p_comments       => NULL,
          p_source         => 
    'begin
      apex_json.open_array;
      for c in ( select * from jsontweets ) loop
        apex_json.open_object;
          apex_json.write( ''timestamp'', to_char( c.ts, ''yyyy/mm/dd hh24:mi:ss'') );
          apex_json.write( ''tweet'', c.tweetjson );
        apex_json.close_object;
      end loop;
      apex_json.close_array;
    end;');

      ORDS.DEFINE_HANDLER(
          p_module_name    => '<Module Name>',
          p_pattern        => '<Pattern Name>',
          p_method         => 'POST',
          p_source_type    => 'plsql/block',
          p_mimes_allowed  => '',
          p_comments       => NULL,
          p_source         => 
    'begin
      insert into jsontweets values (sysdate, :data);
      commit;
    end;');

      ORDS.DEFINE_PARAMETER(
          p_module_name        => '<Module Name>',
          p_pattern            => '<Pattern Name>',
          p_method             => 'POST',
          p_name               => 'data',
          p_bind_variable_name => 'data',
          p_source_type        => 'HEADER',
          p_param_type         => 'STRING',
          p_access_method      => 'IN',
          p_comments           => NULL);

        
            
    COMMIT;

    END;
      ```

- You are going to use this REST endpoint in the Python Application in the next guide .


-   You are now ready to move to the next guide .

<table>
<tr><td class="td-logo">[![]()](#)</td>
<td class="td-banner">
## Great Work - All Done!
</td>
</tr>
<table>