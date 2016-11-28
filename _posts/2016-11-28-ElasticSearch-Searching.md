Get By Example


BoolQueryBuilder queryBuilder = QueryBuilders.boolQuery();
for ( Entry<String, String> searchTerm : request.getSearchCriteria().entrySet() ) {
  queryBuilder.must( QueryBuilders.wildcardQuery( searchTerm.getKey(), searchTerm.getValue() ) );
}

QueryBuilder qb = QueryBuilders.queryStringQuery( queryBuilder.toString() );



boolQuery/must
https://discuss.elastic.co/t/java-api-for-filterbuilders-in-elasticsearch-2/34835/4


term/match/wildcard
https://www.elastic.co/guide/en/elasticsearch/reference/5.0/term-level-queries.html
