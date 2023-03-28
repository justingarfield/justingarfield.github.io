<table>
    <thead>
        <tr>
            <th>Employer</th>
            <th>Title</th>
            <th>Location</th>
            <th>Start Date</th>
            <th>End Date</th>
        </tr>
    </thead>
    <tbody>
        {% for employment in site.data.employmentHistory %}
        <tr>
            <td>{{ employment.employer }}</td>
            <td>{{ employment.title }}</td>
            <td>{{ employment.location }}</td>
            <td>{{ employment.startDate }}</td>
            <td>{{ employment.endDate }}</td>
        </tr>
        {% endfor %}
    </tbody>
</table>
