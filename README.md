# find out current active trasaction

  private static void updateQuery() throws SQLException {
        String sql ...
        }
    }

    private static boolean isCurrentActiveTransaction() throws SQLException {
       

        String sql = "SELECT \n" +
                     "    COUNT(1) AS count\n" +
                     "FROM\n" +
                     "    INFORMATION_SCHEMA.INNODB_TRX\n" +
                     "WHERE\n" +
                     "    trx_mysql_thread_id = CONNECTION_ID()";

       
}
package JDBS;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class commit {

	private static final String url = "jdbc:mysql://localhost:3306/mydb";
	private static final String user = "root";
	private static final String password = "pas";
	private static Connection connection;

	public static void main(String[] args) throws SQLException {
		try {

			connection = DriverManager.getConnection(url, user, password);
			// change auto commit status

			connection.setAutoCommit(false);
			updateQuery();
			// execute update query
			connection.commit();
			System.out.println("ok");
		} catch (Exception e) {
			try {
				// rollback
				connection.rollback();
				System.out.println("rollback");
			} catch (SQLException e1) {
				e1.printStackTrace();
			}
			e.printStackTrace();
		} finally {
			connection.close();
		}
	}

	private static void updateQuery() throws SQLException {
		try (Statement statement = connection.createStatement();
				ResultSet resultSet = statement.executeQuery(
						"SELECT COUNT(1) AS count FROM INFORMATION_SCHEMA.INNODB_TRX WHERE trx_mysql_thread_id = CONNECTION_ID()")) {
			if (resultSet.next() && resultSet.getInt("count") == 0) {
				// No active transaction, perform the update
				try (java.sql.PreparedStatement preparedStatement = connection
						.prepareStatement("UPDATE zoo SET name = ? WHERE species = 'hh' ")) {
					preparedStatement.setInt(1, 25); // Set the new age value
					preparedStatement.executeUpdate();
					System.out.println("Update completed successfully.");
				}
			} else {
				System.out.println("There is an active transaction. Skipping update.");
			}
		}
	}
}
